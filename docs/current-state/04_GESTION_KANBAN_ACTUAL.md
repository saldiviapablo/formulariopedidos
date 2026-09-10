# 04 - Bandeja de Gestión Kanban y Tabla

> **Estado:** `[WEWEB-VERIFICADO]` / `[PROD-VERIFICADO]`  
> **Proyecto WeWeb ID:** `5791a02a-c0b8-49dc-a5b6-afe5fbb53b60`  
> **Fecha de Auditoría:** 2026-09-10  
> **Ruta:** `/gestion` (UID: `a39854ef-f0ad-448f-aa1c-0e9e4a3b7080`)

---

## 1. Propósito y Roles de Acceso

La página `/gestion` es la bandeja principal de trabajo para los operadores del sistema.
- **Acceso:** Protegido por WeWeb Auth Guard (`requireAuth = true`).
- **Roles Autorizados:** `admin`, `equipo_interno`.
- **Comportamiento:**
  - Los usuarios anónimos son redirigidos inmediatamente a `/login`.
  - Si el usuario autenticado tiene rol `admin`, se visualiza en la barra de navegación el botón de acceso directo a `/usuarios`.

---

## 2. Vistas Disponibles: Kanban vs Tabla

La vista principal cuenta con un conmutador (Tabs / Switch) para alternar entre dos modos de visualización:
1. **Modo Kanban (Por Estado de Servicio):**
   - Columnas organizadas por los estados operativos: `Nuevo`, `En revisión`, `Asignado`, `En proceso`, `Esperando información`, `Correcciones`, `Finalizado`, `Cancelado`.
   - Cada tarjeta representa un servicio solicitado dentro de un pedido, mostrando:
     - Badge con número de pedido (`PED-2026-XXXXXX`).
     - Área y tipo de servicio (ícono + texto).
     - Área solicitante (Ministerio/Ente).
     - Badge de responsable asignado (o "Sin asignar").
     - Fecha de ingreso y días transcurridos.
2. **Modo Tabla Consolidada:**
   - Tabla con paginación y ordenamiento por columnas:
     - `ID / Pedido` (Link clickable que navega a `/pedido/:id`).
     - `Fecha de Ingreso` (Formato `DD/MM/YYYY HH:mm`).
     - `Solicitante` (Nombre y Área).
     - `Servicios Incluidos` (Pills con resumen de servicios).
     - `Estado General` (Badge con color de estado).
     - `Acciones` (Botón "Ver Detalle").

---

## 3. Barra de Filtros y Búsqueda

La barra superior incluye controles reactivos vinculados a variables de Pinia:
- **Búsqueda por texto libre (`filter_search`):** Filtra en tiempo real por número de PED, nombre de solicitante o correo.
- **Filtro por Área (`filter_area`):** Desplegable con opciones (`Todas`, `Diseño gráfico`, `Cobertura`, `Gacetilla`, `Redes`).
- **Filtro por Estado (`filter_estado`):** Filtra tarjetas o filas por estado específico.
- **Filtro por Responsable (`filter_responsable`):** Desplegable poblado dinámicamente mediante el backend workflow `api_listar_responsables`.

---

## 4. Selector de Responsable: Contrato y Comportamiento

### 4.1 Backend Workflow `api_listar_responsables`
- **ID Workflow:** `565645b9-79e5-4981-bdd4-763cf076601b`
- **Consulta SQL:**
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
- **Contrato de Binding en el Componente Select:**
  - `item.value` -> UUID de Auth User (`internal_user.id`).
  - `item.label` -> `nombre_usuario` (Ej: `pablo`, `admin_medios`, etc.).
  - **REGLA DE NEGOCIO ESTRICTA:** No se expone el email ni el nombre y apellido en el selector para garantizar el desacoplamiento y privacidad del nuevo modelo de identidad.

---

## 5. Evidencia de Verificación
- `[WEWEB-VERIFICADO]`: Estructura del DOM, componentes de Kanban y Tabla inspeccionados en WeWeb Editor.
- `[CONFIG-VERIFICADO]`: SQL de `api_listar_responsables` verificado en `backend_workflows_dump.json`.
