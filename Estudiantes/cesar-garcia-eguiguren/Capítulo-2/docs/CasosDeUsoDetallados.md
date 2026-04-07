
# Casos de uso detallados

## CU-01 – Autenticarse en el Sistema

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | El `user_id` tiene un `hr_employee` activo vinculado en Odoo. |
| **Postcondición** | JWT almacenado en `localStorage`, cabecera `Authorization: Bearer` inyectada en el cliente Axios. Usuario redirigido a CU-02. |

**Flujo principal:**
1. El actor navega a `/login` e introduce su `res_users.id`.
2. `POST /auth/token {user_id}` → el sistema localiza el empleado activo.
3. `resolve_role_and_scope()` determina rol y calcula `employee_ids`, `department_ids`, `project_ids` mediante CTE recursivo.
4. Se emite JWT `HS256` (8 h) con `{user_id, employee_id, role, employee_ids, department_ids, project_ids}`.
5. El frontend almacena token y redirige a `/`.

**Flujos alternativos:**
- `FA-01` _(paso 2)_: Sin empleado activo → HTTP 404, usuario permanece en login.
- `FA-02` _(paso 3)_: Rol `"empleado"` → acceso denegado, mensaje informativo.
- `FA-03` _(sesión activa)_: Token expirado → interceptor Axios detecta HTTP 401, limpia `localStorage` y redirige a `/login`.

**Relaciones:** *(sin `<<include>>`/`<<extend>>` hacia otros CU)*.  
CU-01 es **precondición** del resto de casos de uso (sesión/JWT), pero **no se modela como `<<include>>`** porque no se inserta como sub-flujo reutilizable dentro de otros CU: se ejecuta una vez para iniciar sesión.

---

### CU-02 – Visualizar Overview del Sistema

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. |
| **Postcondición** | El actor tiene una visión consolidada del estado del sistema y puede navegar a cualquier otro CU. |

**Flujo principal:**
1. El sistema carga en paralelo: `GET /dashboards/overview`, `/charts/task-distribution`, `/charts/productivity-trend?days=14`, `/dashboards/summary/manager`, `/metrics/compliance`, `/metrics/rework-rate`, `/metrics/lead-time`.
2. Se muestran 4 KPIs: proyectos activos, empleados activos, tareas abiertas (+creadas esta semana), tareas vencidas.
3. Si hay tareas vencidas → banner de alerta rojo con enlace a CU-10 (`status=overdue`).
4. Si hay empleados sobrecargados → banner amarillo con sus nombres y enlace a CU-03.
5. Gráfico de área de actividad (14 días) y panel de salud operativa (cumplimiento, retrabajo, lead time con barras de progreso y distribución del equipo).
6. Distribución de tareas por etapa (barras de progreso coloreadas) y acceso rápido a CU-03, CU-08, CU-04, CU-10, CU-20, CU-21.
7. Los accesos rápidos (p.ej. `.../tareas?status=overdue`, `.../tareas?status=pending`, `.../empleados`) son **redirecciones a otras páginas**, no sub-flujos dentro de `/overview`.
8. El Responsable recibe los datos filtrados por su `project_ids` y `employee_ids`.

**Flujos alternativos:**
- `FA-01`: Alguna petición falla → `ErrorState` con botón Reintentar.
- `FA-02`: Sin tareas ni proyectos → KPIs a 0, estados vacíos con iconos.

**Relaciones:**  
`<<include>>` CU-13 (cumplimiento), CU-16 (retrabajo), CU-18 (lead time) *(panel de salud se carga siempre al renderizar `/overview`)*.  
Navega a CU-03, CU-04, CU-08, CU-10, CU-20, CU-21.

---

## CU-03 – Consultar Panel Manager

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. |
| **Postcondición** | El actor conoce el estado operativo completo del equipo. |

**Flujo principal:**
1. El actor navega a `/manager`. Puede filtrar por departamento y alternar entre vista **Ejecutivo** y **Equipo**.
2. El sistema carga: `GET /dashboards/summary/manager` y `GET /departments/`.
3. 4 KPIs: total empleados, sobrecargados, subcargados, sin tareas. Banner de alerta si hay sobrecargados.
4. **Vista Ejecutivo:** radar de salud (5 dimensiones: Cumplimiento, Eficiencia, Velocidad, Calidad, Capacidad), panel de alertas operativas, top 5 más cargados con enlace a CU-05, distribución de estados (pills + bar chart), actividad por departamento con enlace a CU-07.
5. **Vista Equipo:** tabla de carga de trabajo ordenable por % de ocupación, con badge de estado y enlace a CU-05 por empleado.
6. **Opcional (drill-down por estado):** al pinchar un estado (p.ej. "Sobrecargado") el sistema lista empleados de ese estado llamando a `GET /dashboards/summary/manager?page=1&page_size=12&sort_by=employee_name&sort_order=asc&status=sobrecargado`. Desde esa lista se puede navegar a CU-05.

**Flujos alternativos:**
- `FA-01`: Responsable → datos filtrados por `employee_ids` y `department_ids`.
- `FA-02`: Sin empleados en el departamento → tabla vacía.

**Relaciones:** `<<include>>` CU-06 (listar departamentos para filtro). Navega a CU-05, CU-07.

---

## CU-04 – Listar y Buscar Empleados

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. |
| **Postcondición** | El actor localiza el empleado y puede navegar a CU-05. |

**Flujo principal:**
1. `GET /employees/` con parámetros `page`, `page_size`, `search`, `department_id`, `active`, `sort_by`, `sort_order`.
2. Tabla paginada (25/página): nombre, departamento, cargo, email, coste/h, badge de estado.
3. Búsqueda con debounce 300 ms. Filtro por departamento (select). Toggle activos.
4. Ordenación global server-side por cualquier columna.
5. Click en fila → CU-05.
6. El Responsable solo ve empleados de su `employee_ids`.

**Relaciones:** Navega a CU-05.

---

## CU-05 – Consultar Resumen de Empleado

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. El empleado está en el alcance del actor. |
| **Postcondición** | El actor conoce el estado completo del empleado: carga, WIP, productividad y tareas. |

**Flujo principal:**
1. El actor accede a `/employees/{id}` (desde CU-04 o enlace en CU-03/CU-07).
2. El sistema verifica `verify_employee_scope`. Carga: `GET /employees/{id}`, `/dashboards/summary/employee/{id}`.
3. Cabecera con avatar inicial, nombre, cargo, departamento, coste/h y badge de estado de carga.
4. 4 KPIs: tareas pendientes + horas, vencidas sin cerrar, WIP actual, productividad 30d.
5. Sección "Hoy": tarjetas de _tareas asignadas hoy_ y _vencidas sin cerrar_, cada una clickable para abrir CU-10 con los filtros correspondientes pre-rellenados.
6. Sección de tareas en cuatro sub-flujos (pestañas), todos ejecutando CU-10 en modo contextual mediante `GET /tasks/filter`:
   - **Tareas pendientes**: `employee_id=X`, `status=pending`.
   - **Tareas asignadas hoy**: `employee_id=X`, `date_assign=today`.
   - **Ver asignadas**: `employee_id=X` (todas las asignadas al empleado).
   - **Ver responsable**: `employee_id=X`, `responsable=true` (tareas donde el empleado es responsable).
7. Click en tarea → CU-11.

**Flujos alternativos:**
- `FA-01`: El actor intenta acceder a un empleado fuera de su alcance → HTTP 403.
- `FA-02`: Empleado sin `user_id` vinculado → pestañas asignadas y pendientes vacías; workload y WIP a 0.

**Relaciones:** *(este CU es base de extensión)* `<<extend>>` por CU-10 (consulta contextual de tareas). Navega a CU-11.

---

## CU-06 – Listar Departamentos

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. |
| **Postcondición** | El actor localiza el departamento y navega a CU-07. |

**Flujo principal:**
1. `GET /departments/` → cuadrícula de tarjetas con nombre y responsable del departamento.
2. El Responsable solo ve sus `department_ids`.
3. Click en tarjeta → CU-07.

**Relaciones:** Navega a CU-07.

---

## CU-07 – Consultar Resumen de Departamento

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. El departamento está en el alcance del actor. |
| **Postcondición** | El actor conoce el estado de carga del departamento y puede navegar a sus empleados. |

**Flujo principal:**
1. El actor accede a `/departments/{id}` (desde CU-06 o enlace en CU-03).
2. El sistema verifica `verify_department_scope`. Carga: `GET /departments/{id}`, `/departments/{id}/workload-summary`.
3. Cabecera con nombre del departamento y manager.
4. 4 KPIs: total empleados, sobrecargados, subcargados, sin tareas.
5. Banner de alerta rojo si hay sobrecargados (con sus nombres).
6. **Pestaña Carga de trabajo:** tabla ordenable con nombre, tareas pendientes, horas pendientes, % de carga (barra de progreso) y badge de estado. Click en empleado → CU-05.
7. **Pestaña Empleados:** `GET /departments/{id}/employees` → tabla con cargo, email, coste/h y estado. Click en empleado → CU-05.

**Flujos alternativos:**
- `FA-01`: Responsable sin acceso al departamento → HTTP 403.
- `FA-02`: Departamento sin empleados activos → pestañas vacías.

**Relaciones:** Navega a CU-05.

---

## CU-08 – Listar Proyectos

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. |
| **Postcondición** | El actor localiza el proyecto y navega a CU-09. |

**Flujo principal:**
1. `GET /projects/` → cuadrícula de tarjetas con nombre, cliente y código.
2. El Responsable solo ve sus `project_ids`.
3. Click en tarjeta → CU-09.

**Relaciones:** Navega a CU-09.

---

## CU-09 – Consultar Resumen de Proyecto

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. El proyecto está en el alcance del actor. |
| **Postcondición** | El actor conoce el estado completo del proyecto (eficiencia, riesgo, rentabilidad, tareas y equipo). |

**Flujo principal:**
1. El actor accede a `/projects/{id}` (desde CU-08 o resultado de CU-22).
2. El sistema verifica `verify_project_scope`. Carga: `GET /projects/{id}`, `/dashboards/summary/project/{id}`, `/tasks/stages`.
3. Cabecera con nombre, cliente y badges de estado (Rentable/Pérdidas, Riesgo bajo/medio/alto).
4. 4 KPIs: índice de eficiencia, índice de riesgo, rentabilidad %, total tareas.
5. Gráfico de barras: horas estimadas vs reales.
6. **Pestaña Tareas:** ejecuta CU-10 en modo contextual (`project_id=X`). Filtros adicionales de estado y etapa disponibles dentro de la pestaña.
7. **Pestaña Equipo:** `GET /projects/{id}/employees` → empleados con horas imputadas, coste/h. Click en empleado → CU-05.

**Flujos alternativos:**
- `FA-01`: Sin horas imputadas → rentabilidad 0 %, gráfico de horas vacío.
- `FA-02`: Responsable sin acceso al proyecto → HTTP 403.

**Relaciones:** Navega a CU-05 y CU-11.

---

## CU-10 – Listar Tareas

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. |
| **Postcondición** | El actor localiza las tareas buscadas o accede al detalle (CU-11). |

**Flujo principal:**
1. Se ejecuta desde tres contextos posibles:
   - **Global** (`/tasks`): el actor elige filtros manualmente.
   - **Desde CU-09**: `project_id` pre-rellenado.
   - **Desde CU-05**: `employee_id` (y opcionalmente `status`) pre-rellenados.
2. `GET /tasks/filter` con parámetros combinables: `status`, `stage_id`, `project_id`, `employee_id`, `date_from`, `date_to`, `date_assign`, `root_only`, `sort_by`, `sort_order`, `page`, `page_size`.
3. Tabla paginada: nombre de la tarea, etapa (badge), horas estimadas, deadline, fecha cierre, estado (badge).
4. Ordenación global server-side. `stage_id` tiene prioridad sobre `status` cuando ambos están activos.
5. Click en tarea → CU-11.
6. El Responsable tiene sus tareas automáticamente restringidas a sus `project_ids`.

**Flujos alternativos:**
- `FA-01`: Sin tareas para los filtros aplicados → estado vacío con mensaje.
- `FA-02`: Responsable intentando filtrar por proyecto fuera de su alcance → HTTP 403.

**Relaciones:** Navega a CU-11.

---

## CU-11 – Consultar Detalle de Tarea

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. La tarea pertenece a un proyecto en el alcance del actor. |
| **Postcondición** | El actor conoce todos los datos de la tarea. |

**Flujo principal:**
1. `GET /tasks/{id}` → ficha completa de la tarea.
2. **Sección info general:** proyecto (link a CU-09), deadline, fecha fin, fecha asignación.
3. **Sección personas:** responsable (link a CU-05) y empleados asignados (links a CU-05).
4. **Sección horas** _(si `planned_hours > 0`)_: KPIs de horas est./invertidas/restantes, barra de progreso (rojo si >100%), métrica de productividad.
5. **Sección subtareas** _(si las hay)_: lista clickable, cada una abre CU-11 recursivamente.
6. Si `parent_id` existe → enlace a la tarea padre (CU-11).

**Flujos alternativos:**
- `FA-01`: Tarea no encontrada → HTTP 404.
- `FA-02`: Responsable con tarea fuera de sus proyectos → HTTP 403.

**Relaciones:** Navega a CU-05, CU-09, CU-11 (recursivo en subtareas).

---

## CU-12 – Consultar Productividad

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01. |
| **Postcondición** | El actor conoce la eficiencia de ejecución frente a la estimación. |

**Flujo principal:**
1. El actor navega a `/metrics`. **Al entrar no se llama a ninguna métrica por defecto**; solo se cargan datos de apoyo para filtros: `GET /employees/?page_size=200`, `GET /projects/`.
2. Al pinchar en la tarjeta/sección "Productividad" se ejecuta `GET /metrics/productivity` (con filtros opcionales por empleado/proyecto/rango de fechas).
3. Gauge de % promedio + total tareas analizadas.
4. Gráfico de barras horizontal: top 8 tareas por productividad individual.
5. El Responsable sin filtros recibe el agregado ponderado de todos sus proyectos.

**Relaciones:** Accesible desde `/metrics`.

---

## CU-13 – Consultar Cumplimiento de Plazos

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01. |
| **Postcondición** | El actor conoce la fiabilidad de entrega a tiempo. |

**Flujo principal:**
1. `GET /metrics/compliance` (filtros: `employee_id`, `project_id`, `root_only`) → `compliance_rate = (on_time / total) × 100` donde `on_time` = `date_end ≤ date_deadline`.
2. Doughnut chart central con % y KPIs (a tiempo, con retraso, total).

**Relaciones:** `<<include>>` desde CU-02 (panel de salud en `/overview`).

---

## CU-28 – Consultar Carga de Trabajo (Workload) de Empleado

| Campo             | Valor                                               |
| ----------------- | --------------------------------------------------- |
| **Actores**       | Director, Responsable                               |
| **Precondición**  | CU-01. Empleado seleccionado.                       |
| **Postcondición** | El actor identifica empleados con sobre/infracarga. |

**Flujo principal:**

1. El actor navega a `/metrics` y selecciona un empleado.
2. *(Condición: “Workload” seleccionada)* El sistema ejecuta `GET /metrics/workload?employee_id=X&detailed=false&root_only=false`.
3. El sistema calcula `workload_pct = (horas_pendientes / 40h) × 100` y deriva el estado:
   - `sobrecargado` si `workload_pct > 120%`
   - `normal` si `70% ≤ workload_pct ≤ 120%`
   - `subcargado` si `workload_pct < 70%`
4. Se muestra: gauge de workload %, barras de horas y badge de estado.

**Flujos alternativos:**
- `FA-01`: Empleado fuera de alcance (Responsable) → HTTP 403.
- `FA-02`: Sin tareas pendientes → `pending_hours = 0`, estado `without_tasks` (cuando aplica en dashboards) o `subcargado`/0% en vista métrica.

**Relaciones:** Accesible desde `/metrics`. *(Además, su resultado se reutiliza en dashboards mediante `GET /dashboards/summary/employee/{id}` y `GET /dashboards/summary/manager`.)*

---

## CU-14 – Consultar WIP (Work In Progress) de Empleado

| Campo             | Valor                                                                        |
| ----------------- | ---------------------------------------------------------------------------- |
| **Actores**       | Director, Responsable                                                        |
| **Precondición**  | CU-01. Empleado seleccionado.                                                |
| **Postcondición** | El actor identifica el nivel de paralelismo de tareas abiertas del empleado. |

**Flujo principal:**

1. El actor selecciona un empleado en `/metrics`.
2. `GET /metrics/wip?employee_id=X` → número de tareas abiertas asignadas.

   * Umbrales: óptimo ≤3, aceptable ≤5, sobrecargado >5.
3. Se muestra: badge de estado y recomendación textual sobre WIP.

**Relaciones:** Accesible desde `/metrics`.

---

## CU-15 – Consultar Riesgo de Proyecto

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01. Proyecto en alcance del actor. |
| **Postcondición** | El actor identifica proyectos con alta probabilidad de incumplimiento. |

**Flujo principal:**
1. El actor selecciona proyecto en `/metrics`.
2. `GET /metrics/risk-index?project_id=X` → `risk = (en_riesgo / abiertas) × 100`. Una tarea está en riesgo si: está vencida, o ha consumido ≥80 % del plazo desde `date_assign`.
3. Doughnut chart (en riesgo vs en plazo), KPIs y semáforo bajo/medio/alto.

**Relaciones:** Accesible desde `/metrics`.

---

## CU-16 – Consultar Tasa de Retrabajo

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01. |
| **Postcondición** | El actor detecta problemas de calidad o proceso. |

**Flujo principal:**
1. `GET /metrics/rework-rate` (filtros: `project_id`, `employee_id`) → análisis de cambios de etapa en `mail_tracking_value`; una tarea reabierta es la que pasa de etapa cerrada a etapa abierta.
2. `rework_rate = (reabiertos / cerradas_total) × 100`. Semáforo: <8 % verde, 8–15 % naranja, >15 % rojo.

**Relaciones:** `<<include>>` desde CU-02 (panel de salud en `/overview`).

---

## CU-17 – Consultar Exactitud de Estimación

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01. Empleado (responsable de tarea) seleccionado. |
| **Postcondición** | El actor calibra la fiabilidad de las estimaciones del responsable. |

**Flujo principal:**
1. `GET /metrics/estimation-accuracy?responsable_id=X` → ratio medio `real/planificado` para tareas cerradas del responsable con horas > 0.
2. Sesgo: `subestima` (>110 %), `sobreestima` (<90 %), `preciso` (90–110 %).
3. Gauge, badge de sesgo con color, ratio promedio, total tareas y mensaje contextual.

**Relaciones:** Accesible desde `/metrics`.

---

## CU-18 – Consultar Lead Time

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01. |
| **Postcondición** | El actor identifica el tiempo medio de ciclo de las tareas. |

**Flujo principal:**
1. El actor accede a la métrica en `/metrics` o desde un resumen de entidad (CU-05, CU-07, CU-09).
2. `GET /metrics/lead-time` (filtros opcionales: `project_id`, `employee_id`, `date_from`, `date_to`) → días medios entre `date_assign` y `date_end` de tareas cerradas.
3. Gauge con valor (ej: 4.2 días), semáforo (verde <5d, naranja 5-10d, rojo >10d), total de tareas analizadas.
4. Serie temporal (opcional): línea de lead time por semana/mes.

**Flujos alternativos:**
- `FA-01`: Sin tareas cerradas → valor a N/A con mensaje "No hay datos".

**Relaciones:** `<<include>>` desde CU-02 (panel de salud en `/overview`).

---

## CU-19 – Consultar Tiempo por Estado

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01. |
| **Postcondición** | El actor identifica etapas con tiempos excesivos de permanencia. |

**Flujo principal:**
1. `GET /metrics/state-time` (filtros opcionales: `project_id`, `employee_id`) → tabla de etapas con horas medias de permanencia (calculado a partir de `mail_tracking_value`).
2. Tabla: Etapa, Horas medias, # tareas, % del total. Ordenable por horas.
3. Gráfico de barras horizontal: top 10 etapas por tiempo.

**Flujos alternativos:**
- `FA-01`: Sin datos de tracking → tabla vacía.

**Relaciones:** Accesible desde `/metrics`.

---

## CU-20 – Consultar Tareas Canceladas

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01. |
| **Postcondición** | El actor evalúa la tasa de cancelación del proceso. |

**Flujo principal:**
1. `GET /metrics/cancelled-tasks` (filtros opcionales: `project_id`, `employee_id`) → % de tareas cuya etapa se llama "Cancelado".
2. KPI con porcentaje, total de tareas canceladas, semáforo (verde <5%, naranja 5-10%, rojo >10%).
3. Evolución temporal (línea) y distribución por proyecto/empleado (opcional).

**Flujos alternativos:**
- `FA-01`: Sin tareas → canceladas 0 %.

**Relaciones:** Accesible desde `/metrics`.

---

## CU-21 – Consultar Tiempo por Prioridad

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01. |
| **Postcondición** | El actor verifica que el tiempo invertido se alinea con la prioridad de tareas. |

**Flujo principal:**
1. `GET /metrics/priority-time` (filtros opcionales: `project_id`, `employee_id`) → horas medias invertidas por nivel de prioridad (Normal / Urgente / Crítica si aplica).
2. Gráfico de barras agrupadas: Normal vs Urgente, con media esperada (línea de referencia) y desviación.
3. KPIs: horas totales en urgentes, % del tiempo en urgentes.

**Flujos alternativos:**
- `FA-01`: Sin tareas clasificadas → gráfico vacío.

**Relaciones:** Accesible desde `/metrics`.

---

## CU-22 – Visualizar Gráficos Analíticos

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01. |
| **Postcondición** | El actor analiza tendencias y distribuciones visualmente. |

**Flujo principal:**
1. El actor navega a `/graficos`. Selecciona rango de fechas y opcionalmente filtra por empleado, departamento o proyecto.
2. Se cargan datos base y gráficos: `GET /employees/`, `GET /departments/`, `GET /projects/`, `GET /charts/task-evolution?date_from=...&date_to=...&group_by=month`, `GET /charts/task-distribution?date_from=...&date_to=...`, `GET /metrics/client-distribution?date_from=...&date_to=...`.
3. El actor puede cambiar la agrupación temporal semana/mes y aplicar filtros (empleado/departamento/proyecto), refrescando el endpoint de evolución. La distribución por estado y la distribución por cliente se recalculan según el rango de fechas.

**Flujos alternativos:**
- `FA-01`: Sin datos → cada gráfico muestra "Sin datos en el período".
- `FA-02`: El Responsable no ve `client-distribution`; los demás filtran por su alcance.

**Relaciones:** `<<include>>` CU-04 (lista de empleados para filtro), `<<include>>` CU-06 (lista de departamentos para filtro), `<<include>>` CU-08 (lista de proyectos para filtro). Navega a CU-09.

---

## CU-23 – Consultar Asistencia vs Imputaciones

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01. |
| **Postcondición** | El actor detecta discrepancias entre presencia física e imputaciones en partes analíticos. |

**Flujo principal:**
1. El actor navega a `/asistencia`. Rango por defecto: **mes actual**.
2. El sistema carga: `GET /employees/managers/list`, `GET /employees/attendance/comparison?date_from=...&date_to=...`, `GET /employees/?page_size=200`, `GET /departments/`.
3. Se renderiza tabla por empleado con horas fichadas, imputadas, diferencia y `coverage_pct = (imputadas/fichadas)×100` con badge (OK ≥95 %, Revisar ≥80 %, Alerta <80 %).
4. **Opcional (modo Por Responsable):** al cambiar el modo se llama a `GET /employees/attendance/manager/{id}?date_from=...&date_to=...`.
5. **Opcional (detalle diario):** al seleccionar un empleado se llama a `GET /employees/attendance/{id}/daily?date_from=...&date_to=...` y se muestra el gráfico en la propia página.

**Relaciones:** `<<include>>` CU-04 (lista de empleados para filtro), `<<include>>` CU-06 (lista de departamentos para filtro). *(Opcional: modo “Por Responsable” usa `GET /employees/attendance/manager/{id}`.)*

---

## CU-24 – Consultar Rentabilidad Financiera

| Campo | Valor |
|---|---|
| **Actores** | **Director** (exclusivo) |
| **Precondición** | CU-01 con `role = "director"`. |
| **Postcondición** | El Director conoce la rentabilidad real por proyecto, cliente y responsable, y puede planificar la asignación de recursos. |

**Flujo principal:**
1. El actor navega a `/rentabilidad`. Selecciona rango de fechas y modo de filtro: Global, Por Proyecto, Por Responsable.
2. Base de datos: `account_analytic_line.amount` (positivo = ingreso, negativo = gasto).
3. `GET /metrics/profitability/summary` → 4 KPIs globales: ingresos, gastos, neto, rentabilidad %.
4. Gráfico de barras agrupadas (ingresos vs gastos por proyecto, top 12) y donut de estados (Ganancia/Neutro/Pérdida).
5. Tabla de proyectos (`/metrics/profitability/per-project`) con income, expense, net, horas y badge.
6. Tabla de clientes (`/metrics/profitability/per-client`) como sub-flujo (equivalente al antiguo CU-22).
7. Drill-down por responsable: seleccionar manager → botón "Ver detalle" → `/metrics/profitability/manager/{id}` → vista individual con sus proyectos.
8. *(Opcional / WIP de documentación)* Panel **Estado del equipo**: actualmente no existe el endpoint `GET /dashboards/team-assignment` en backend; el estado del equipo se obtiene desde `GET /dashboards/summary/manager` (CU-03).

**Flujos alternativos:**
- `FA-01`: Sin partes analíticos en el período → todo a 0.
- `FA-02`: Un Responsable intenta acceder → HTTP 403, pantalla "Acceso restringido".

**Relaciones:** *(este CU es base de extensión)* `<<extend>>` por CU-26/27 (drill-down de líneas por proyecto/cliente) y vista por responsable (selección de manager → `/metrics/profitability/manager/{id}`).

---

## CU-26 – Consultar Líneas Analíticas de Proyecto

| Campo | Valor |
|---|---|
| **Actores** | **Director** (exclusivo) |
| **Precondición** | CU-01 con `role = "director"`. Proyecto seleccionado en CU-24. |
| **Postcondición** | El Director conoce los ingresos y gastos individuales del proyecto. |

**Flujo principal:**
1. Desde CU-24, el actor hace click en "Ver Detalles" en una fila de proyecto.
2. `GET /metrics/profitability/per-project/{project_id}/lines` → lista de `account_analytic_line` del proyecto, separadas en ingresos (positivos) y gastos (negativos).
3. Panel con dos tablas: Ingresos (fecha, nombre, importe, horas) y Gastos (igual).
4. Totales calculados automáticamente en el frontend.

**Flujos alternativos:**
- `FA-01`: Sin líneas → tablas vacías.

**Relaciones:** `<<extend>>` desde CU-24.

---

## CU-27 – Consultar Líneas Analíticas de Cliente

| Campo | Valor |
|---|---|
| **Actores** | **Director** (exclusivo) |
| **Precondición** | CU-01 con `role = "director"`. Cliente seleccionado en CU-24. |
| **Postcondición** | El Director conoce los ingresos y gastos individuales del cliente (agregados de sus proyectos). |

**Flujo principal:**
1. Desde CU-24, el actor hace click en "Ver Detalles" en una fila de cliente.
2. `GET /metrics/profitability/per-client/{client_id}/lines` → lista agregada de `account_analytic_line` de todos los proyectos del cliente, separadas en ingresos y gastos.
3. Panel con dos tablas: Ingresos y Gastos, con columnas fecha, nombre, importe, horas.
4. Totales por grupo (ingresos totales, gastos totales).

**Flujos alternativos:**
- `FA-01`: Cliente sin proyectos con líneas → tablas vacías.

**Relaciones:** `<<extend>>` desde CU-24.

---

## CU-25 – Realizar Búsqueda Global

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01. Mínimo 2 caracteres de entrada. |
| **Postcondición** | El actor localiza el recurso y navega a su resumen. |

**Flujo principal:**
1. El actor navega a `/search` (o atajo en sidebar).
2. Escribe ≥2 caracteres; debounce de 350 ms.
3. `GET /search/?q=…&entity=all` → resultados agrupados: tareas, proyectos, empleados (máx. 10 por tipo).
4. El actor puede filtrar por tipo de entidad con los botones de pestaña.
5. Click en tarea → CU-11; en proyecto → CU-09; en empleado → CU-05.
6. El Responsable recibe resultados filtrados por su alcance.

**Relaciones:** Navega a CU-05, CU-09, CU-11.

---

