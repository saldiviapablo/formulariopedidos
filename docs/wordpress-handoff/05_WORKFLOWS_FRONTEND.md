# 05. Workflows de Frontend e Interacciones de Usuario

Este documento describe de forma exhaustiva todos los flujos interactivos del cliente (Frontend Workflows en WeWeb), incluyendo disparadores (triggers), gestión de variables locales, transformaciones de datos, validaciones y bifurcaciones de error.

---

## 1. Mapa General de Workflows Frontend

```mermaid
graph TD
    subgraph Publico [/nueva-solicitud, /seguimiento, /completar-solicitud]
        WF_NS[wf_submit_request: Envío de Nueva Solicitud]
        WF_SEG[wf_track_search: Búsqueda y visualización de Pedido]
        WF_COMP[wf_reply_info: Envío de Aclaraciones con Token]
    end

    subgraph Auth [/login, /solicitar-acceso]
        WF_REG[wf_register_access: Registro con validación 2-30 chars]
        WF_LOG[wf_login: Autenticación y Redirección por Rol]
    end

    subgraph Gestion [/gestion, /gestion/pedido/:id/detalle]
        WF_FIL[wf_filter_change: Filtrado reactivo en bandeja]
        WF_ASG[wf_quick_assign: Asignación rápida de Responsable]
        WF_EST[wf_status_change: Cambio de Estado de Pedido/Servicio]
        WF_SOL_INF[wf_request_info: Crear Solicitud de Aclaración]
        WF_SND_EML[wf_send_custom_email: Envío de email vía n8n]
        WF_USR_ADM[wf_admin_user_manage: Aprobar/Revocar/Editar Usuario]
    end
```

---

## 2. Detalle de Workflows Frontend

### 2.1. `wf_submit_request` (Pantalla `/nueva-solicitud`)
- **Disparador**: Evento `On Click` del botón "Confirmar y Enviar Solicitud" (Paso 3).
- **Variables Involucradas**:
  - `var_solicitante_contacto`: Objeto `{ nombre, apellido, email, telefono, cargo, area_id }`.
  - `var_servicios_seleccionados`: Array de objetos `[ { tipo_id, especificacion: { ... } } ]`.
  - `var_archivos_adjuntos`: Array de objetos subidos a WeWeb Storage.
  - `var_ui_is_submitting`: Boolean de loading / spinner.
- **Flujo de Ejecución**:
  1. Activa `var_ui_is_submitting = true`.
  2. Valida integridad de campos obligatorios en cada servicio seleccionado.
  3. Estructura el payload consolidado para la API.
  4. Invoca backend workflow: `api_crear_pedido_completo` (`819b8329-94b8-4a7d-929f-ce7a477c9161`).
  5. **Rama Éxito**:
     - Guarda los PEDs retornados en `var_resultado_pedidos`.
     - Cambia el paso del wizard a `step = 4` (Pantalla de Éxito).
     - Resetea las variables del formulario.
  6. **Rama Error**:
     - Muestra toast / modal de error detallando el motivo (ej. "Error en almacenamiento" o "Área no disponible").
  7. Desactiva `var_ui_is_submitting = false`.

---

### 2.2. `wf_register_access` (Pantalla `/solicitar-acceso`)
- **Disparador**: Evento `On Click` del botón "Registrarme / Solicitar Acceso".
- **Variables**:
  - `var_reg_nombre`, `var_reg_apellido`, `var_reg_username`, `var_reg_email`, `var_reg_pass`, `var_reg_pass_conf`.
- **Validaciones en Cliente**:
  - `var_reg_username`: Longitud entre 2 y 30 caracteres. Regex: `^[a-zA-Z0-9._-]+$`. Se transforma a `trim().toLowerCase()`.
  - `var_reg_pass == var_reg_pass_conf` y longitud >= 8.
- **Acción Backend**:
  - Llama a `api_registrar_solicitud_acceso` (`10ee0061-c651-456a-8573-ade4c43e37b0`).
- **Post-Ejecución**:
  - Si éxito -> Redirección inmediata a `/acceso-pendiente`.
  - Si error de unicidad (409) -> Muestra mensaje: "El usuario o correo electrónico ya se encuentra registrado".

---

### 2.3. `wf_quick_assign` (Pantalla `/gestion`)
- **Disparador**: Evento `On Change` del selector Dropdown de Responsable en cualquier fila de la tabla de pedidos.
- **Binding del Selector**:
  - `options`: Colección alimentada por `api_listar_responsables` (`565645b9-79e5-4981-bdd4-763cf076601b`).
  - `label`: `item.nombre_usuario` (o `sin_usuario`).
  - `value`: `item.user_id` (Auth UUID).
- **Flujo**:
  1. Captura `selected_user_id` y `pedido_id`.
  2. Ejecuta `api_actualizar_servicio` (`ec935c4c-4c71-4d1a-9ea5-fecc8b908312`).
  3. Muestra badge de actualización inmediata (optimistic UI update).
  4. Refresca la vista `vw_servicios_gestion`.

---

### 2.4. `wf_send_custom_email` (Pantalla `/gestion/pedido/:id/detalle`)
- **Disparador**: Evento `On Click` en "Enviar Comunicación" (Tab Comunicaciones).
- **Flujo**:
  1. Valida que `asunto` y `cuerpo` no estén vacíos.
  2. Ejecuta backend workflow `bw_enviar_comunicacion_pedido` (`5ebf567a-1a5b-4e8d-916d-51c97073b865`).
  3. El backend invoca el Webhook n8n (`pedidos-email`) enviando el correo institucional.
  4. Agrega la nueva fila al historial visual de comunicaciones.
  5. Limpia los campos del formulario.
