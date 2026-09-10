# 12 - Comunicaciones, n8n y Notificaciones por Correo

> **Estado:** `[WEWEB-VERIFICADO]` / `[CONFIG-VERIFICADO]`  
> **Proyecto WeWeb ID:** `5791a02a-c0b8-49dc-a5b6-afe5fbb53b60`  
> **Fecha de Auditoría:** 2026-09-10

---

## 1. Arquitectura de Notificaciones y Comunicaciones

El sistema centraliza todas las comunicaciones en el backend workflow `bw_enviar_comunicacion_pedido` (`8e32cb71-f921-470a-a712-4091a679efb5`).

```mermaid
graph TD
    Trigger[Evento: Nuevo Pedido / Cambio Estado / Info / Entrega] --> Hub[bw_enviar_comunicacion_pedido]
    
    Hub -->|tipo == 'pedido_ingresado'| N8N[Disparar n8n Native Trigger]
    N8N --> JWT[Generar JWT HS256 Efímero]
    JWT --> Hook[Webhook n8n de la Secretaría]
    
    Hub -->|tipo != 'pedido_ingresado'| Resend[Despachar Email vía WeWeb Resend/SMTP]
    Resend --> Solicitante[Correo al Solicitante]
    
    Hub --> Log[Insertar en tabla comunicaciones_pedido]
```

---

## 2. Integración con n8n (`pedido_ingresado`)

Para el ingreso de nuevos pedidos, WeWeb dispara la integración nativa de n8n:
- **Variable de Secreto:** `N8N_PEDIDOS_JWT_SECRET_V2` (Environment Variable).
- **Token JWT:**
  - Algoritmo: `HS256`.
  - Expiración: 5 minutos (`exp = now() + 300`).
  - Payload:
    ```json
    {
      "communication_id": "uuid-v7",
      "pedido_id": "uuid-pedido",
      "pedido_visible": "PED-2026-000101",
      "tipo": "pedido_ingresado",
      "jti": "random-nonce"
    }
    ```
- **Webhook Target:** Configurado en la variable `N8N_WEBHOOK_URL_PEDIDOS`.

---

## 3. Plantillas de Correo Transaccional

Para el resto de los eventos, el sistema utiliza el plugin de envío de correo de WeWeb (Resend / SMTP) con las siguientes plantillas HTML:

1. **`informacion_faltante`:**
   - Asunto: `Información requerida para su solicitud {{pedido_visible}}`
   - Contenido: Detalle del requerimiento solicitado por el operador y botón CTA hacia `https://secretariamedios-production.weweb.io/solicitud-informacion?token={{token}}`.
2. **`cambio_estado`:**
   - Asunto: `Actualización de estado en su pedido {{pedido_visible}}`
   - Contenido: Nuevo estado del servicio (`En proceso`, `Asignado`, etc.) y enlace a `/seguimiento`.
3. **`finalizado`:**
   - Asunto: `Su solicitud {{pedido_visible}} ha sido finalizada`
   - Contenido: Enlace directo de descarga (`producto_final_url`) y notas de entrega del operador.
4. **`cancelado`:**
   - Asunto: `Cancelación de servicio en pedido {{pedido_visible}}`
   - Contenido: Motivo detallado de la cancelación.

---

## 4. Auditoría en Base de Datos (`comunicaciones_pedido`)
Cada intento de envío registra de forma inmediata:
- `pedido`, `servicio`, `tipo`, `destinatario`, `asunto`, `mensaje`, `estado` (`enviado` o `fallido`), `provider_message_id` y `error` en caso de falla.

---

## 5. Evidencia de Verificación
- `[CONFIG-VERIFICADO]`: Código JavaScript y SQL de `bw_enviar_comunicacion_pedido` extraído de los volcados de workflows.
