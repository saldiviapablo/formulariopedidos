# 04 - Bandeja de Gestión Kanban y Tabla

> **Estado:** `[WEWEB-VERIFICADO]` / `[PROD-VERIFICADO]`  
> **Proyecto WeWeb ID:** `5791a02a-c0b8-49dc-a5b6-afe5fbb53b60`  
> **Fecha de Auditoría:** 2026-09-10  
> **Ruta:** `/gestion` (UID: `a39854ef-f0ad-448f-aa1c-0e9e4a3b7080`)

---

## 1. Propósito y Roles de Acceso

La página `/gestion` es la interfaz operativa principal para el equipo de la Secretaría de Medios.
- **Acceso:** Protegido por Guard de Autenticación (`requireAuth = true`). `[WEWEB-VERIFICADO]`
- **Roles Permitidos:** `admin`, `equipo_interno`. `[WEWEB-VERIFICADO]`
- **Comportamiento:** Redirige a `/login` si el usuario no cuenta con sesión activa.

---

## 2. Vistas: Modo Kanban vs Modo Tabla

La interfaz ofrece un switch reactivo para alternar entre dos modos de visualización: `[WEWEB-VERIFICADO]`

### 2.1 Modo Kanban (Agrupado por Estado de Servicio)
- **Columnas:** Organizadas por los estados de `servicios_solicitados.estado`: `Nuevo`, `En revisión`, `Asignado`, `En proceso`, `Esperando información`, `Correcciones`, `Finalizado`, `Cancelado`.
- **Tarjetas de Servicio:** Cada tarjeta representa una línea de servicio solicitada dentro de un pedido, mostrando:
  - Badge con número de pedido (`PED-2026-XXXXXX`).
  - Tipo de servicio y área.
  - Organismo solicitante (`area_solicitante`).
  - Responsable asignado (`nombre_usuario` o "Sin asignar").
  - Fecha de ingreso y antigüedad.
- **Interacción y Drag & Drop:** `[WEWEB-VERIFICADO]`  
  *No existe soporte de Drag & Drop HTML5 nativo.* El cambio de estado o asignación de responsable se realiza abriendo la tarjeta (navegación a `/pedido/:id` o mediante drawer modal) y seleccionando el nuevo valor en el selector correspondiente.

### 2.2 Modo Tabla Consolidada
- Lista tabular con paginación que agrupa por cabecera de pedido (`pedidos`), mostrando número de PED, fecha de creación, solicitante, badges de servicios incluidos y estado general (`pedidos.estado_general`).

---

## 3. Barra de Filtros y Búsqueda

- **Búsqueda libre (`filter_search`):** Filtra en tiempo real por número de PED, solicitante o correo. `[WEWEB-VERIFICADO]`
- **Filtro por Área (`filter_area`):** Desplegable con opciones de áreas de producción. `[WEWEB-VERIFICADO]`
- **Filtro por Estado (`filter_estado`):** Filtra tarjetas por estado operativo. `[WEWEB-VERIFICADO]`
- **Filtro por Responsable (`filter_responsable`):** Desplegable poblado dinámicamente mediante `api_listar_responsables`. `[WEWEB-VERIFICADO]`

---

## 4. Selector de Responsable: Contrato de Datos

### 4.1 Backend Workflow `api_listar_responsables` (`565645b9-79e5-4981-bdd4-763cf076601b`)
- **SQL Verificado:** `[CONFIG-VERIFICADO]`
  ```sql
  SELECT 
    internal_user.id AS value,
    COALESCE(NULLIF(BTRIM(access_profile.nombre_usuario), ''), 'sin_usuario') AS label,
    COALESCE(NULLIF(BTRIM(access_profile.nombre_usuario), ''), 'sin_usuario') AS nombre_usuario,
    internal_user.id AS user_id
  FROM auth.users AS internal_user
  JOIN public.usuarios_acceso AS access_profile 
    ON access_profile.usuario = internal_user.id
  WHERE access_profile.estado_acceso = 'aprobado'
  ORDER BY access_profile.nombre_usuario ASC;
  ```
- **Contrato de Binding en UI:** `[WEWEB-VERIFICADO]`
  - `item.value` -> UUID de Auth User (`internal_user.id`).
  - `item.label` -> `nombre_usuario` normalizado (ej: `operador_1`, `gestor_medios`).
  - **REGLA DE SEGURIDAD Y PRIVACIDAD:** No se expone el correo electrónico ni nombres personales en el selector.

---

## 5. Evidencia de Verificación
- `[WEWEB-VERIFICADO]`: Componentes de Kanban y Tabla inspeccionados en WeWeb Editor.
- `[CONFIG-VERIFICADO]`: Consulta SQL y lógica de `api_listar_responsables` verificadas en los volcados de backend workflows.
