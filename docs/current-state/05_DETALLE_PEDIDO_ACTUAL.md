# 05 - Detalle de Pedido y Operaciones de Gestión

> **Estado:** `[WEWEB-VERIFICADO]` / `[PROD-VERIFICADO]`  
> **Proyecto WeWeb ID:** `5791a02a-c0b8-49dc-a5b6-afe5fbb53b60`  
> **Fecha de Auditoría:** 2026-09-10  
> **Ruta:** `/pedido/:id` (UID: `48ba972e-d09f-4318-971c-3220fe4ae4ef`)

---

## 1. Estructura y Navegación de la Vista Detalle

La vista `/pedido/:id` es el centro de control operativo de cada solicitud. Al cargar:
1. Extrae el parámetro `:id` (UUID del pedido).
2. Ejecuta el backend workflow `api_obtener_detalle_pedido` (`a3bd5a7e-ca28-4e89-8d14-1cb8ff8fef1c`).
3. Renderiza la cabecera principal y 5 pestañas temáticas:

```mermaid
graph TD
    Cabecera[Cabecera: PED-2026-XXXXXX | Solicitante | Estado General]
    Cabecera --> T1[Pestaña 1: Informacion General]
    Cabecera --> T2[Pestaña 2: Servicios Solicitados y Estados]
    Cabecera --> T3[Pestaña 3: Solicitar Informacion Faltante]
    Cabecera --> T4[Pestaña 4: Entrega Final y Finalizacion]
    Cabecera --> T5[Pestaña 5: Historial y Comunicaciones]
```

---

## 2. Pestaña 1 — Información General
- **Datos del Solicitante:** Nombre completo, Correo electrónico, Teléfono de contacto, Área / Ministerio solicitante.
- **Trazabilidad:** Fecha y hora exacta de creación (`createdAt`), Submission Token, Enlace público de seguimiento para compartir.
- **Sincronización Notion:** Badge de estado de sincronización (`notion_sync_status`), URL de la página en Notion (`notion_url`), fecha del último sync.

---

## 3. Pestaña 2 — Gestión de Servicios Solicitados
Cada servicio solicitado se renderiza en una tarjeta individual con controles directos:
1. **Asignación de Responsable:**
   - Dropdown poblado por `api_listar_responsables`.
   - Permite seleccionar o cambiar el operador asignado.
   - Ejecuta `api_asignar_responsable_servicio` (`76b9f273-0aa7-4b71-9252-09292b234473`).
2. **Cambio de Estado del Servicio:**
   - Selector de estado: `Nuevo`, `En revisión`, `Asignado`, `En proceso`, `Esperando información`, `Correcciones`, `Finalizado`, `Cancelado`.
   - Si se selecciona `Cancelado`: Abre un modal exigiendo el campo obligatorio `motivo_cancelacion`.
   - Si se selecciona `Finalizado`: Abre el modal de entrega final (ver Pestaña 4).
   - Ejecuta `api_actualizar_servicio` (`fa8c6c59-efd5-45cf-a734-758fae4d7705`).
3. **Observaciones Internas:**
   - Área de texto para notas confidenciales entre operadores (`observaciones_internas`).

---

## 4. Pestaña 3 — Solicitar Información Faltante ([CONTRADICCIÓN RESUELTA])

### 4.1 Comportamiento y Regla de Negocio
- **UI:** Dispone de un área de texto con la leyenda explicativa: *"Solicitá la información necesaria sin cambiar el estado del servicio."*
- **Backend Workflow:** `api_solicitar_informacion` (`44ad1bdd-c7e8-45a7-9555-ed11931b30c7`).
- **Lógica de Ejecución Verificada:**
  1. Genera un token aleatorio criptográfico y computa su hash SHA-256 (`token_hash`).
  2. Inserta un registro en la tabla `solicitudes_informacion` con vigencia de **15 días** (`expires_at = NOW() + INTERVAL '15 days'`) y `estado = 'pendiente'`.
  3. Ejecuta `bw_enviar_comunicacion_pedido` con `tipo = 'informacion_faltante'` para despachar el correo electrónico al solicitante con el botón *"Completar información requerida"*.
  4. **RESOLUCIÓN TÉCNICA:** Ni el SQL ni el workflow modifican el estado de `pedidos` ni de `servicios_solicitados`. El estado del servicio permanece intacto salvo que el operador decida moverlo manualmente.

---

## 5. Pestaña 4 — Entrega Final y Finalización

Para marcar un servicio como `Finalizado`, el sistema exige la carga de la entrega final:
1. **Enlace al Producto Final (`producto_final_url`):** Obligatorio. Debe ser una URL HTTPS válida (Google Drive institucional, enlace WeTransfer, etc.).
2. **Nota de Entrega (`producto_final_nota`):** Mensaje explicativo para el solicitante.
3. **Ejecución Backend:**
   - Invoca `api_actualizar_entrega_final` (`1a00a1fa-1f4a-436f-8012-ba781d4a0ea2`).
   - Actualiza la tabla `servicios_solicitados`: `estado = 'Finalizado'`, `producto_final_url`, `producto_final_nota`, `updatedAt = NOW()`.
   - Dispara `bw_enviar_comunicacion_pedido` con `tipo = 'finalizado'`, enviando el email con los enlaces descargables al solicitante.

---

## 6. Pestaña 5 — Historial y Comunicaciones

Muestra la auditoría cronológica de todos los correos e interacciones enviadas para el pedido:
- Lista extraída de `comunicaciones_pedido`.
- Muestra: Tipo de comunicación (`pedido_ingresado`, `cambio_estado`, `informacion_faltante`, `finalizado`, `cancelado`), Fecha de envío, Destinatario, Asunto, Estado (`enviado` / `fallido`), y Detalle de error si hubiese.

---

## 7. Evidencia de Verificación
- `[WEWEB-VERIFICADO]`: Componentes y tabs inspeccionados en la página `48ba972e-d09f-4318-971c-3220fe4ae4ef`.
- `[CONFIG-VERIFICADO]`: SQL y triggers de `api_obtener_detalle_pedido` y `api_solicitar_informacion` confirmados en los volcados.
