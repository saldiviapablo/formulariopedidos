# Changelog

## 2026-09-03 — Desarrollo funcional completo y QA aprobado en Editor

- Se aprobó el QA funcional integral de formulario, adjuntos, gestión, estados, producto final, comunicaciones y seguimiento público.
- Se cerró el desarrollo funcional en Editor; UX/UI continúa como una fase separada.
- Production no fue modificada ni publicada.

## 2026-09-03 — Seguimiento público y emails personalizados completados en Editor

- Se implementó y aprobó manualmente la consulta pública de seguimiento mediante PED y correo, sin exponer datos internos.
- Las comunicaciones transaccionales personalizan el saludo y centralizan el acceso temporal de Editor a seguimiento.
- Quedan pendientes rate limiting, dominio/remitente y URL definitivos de Production, publicación Production y QA final general.

## 2026-09-03 — Resend y comunicaciones completados en Editor

- Se implementaron comunicaciones transaccionales e historial interno para Pedido ingresado, En revisión, En proceso, Finalizado, Cancelado y Solicitar información faltante.
- La actualización de estado conserva la operación de negocio aunque falle el envío de correo; el resultado queda trazado en el historial.
- Se comprobaron manualmente en Gmail siete emails de QA.
- Siguen pendientes `/seguimiento`, remitente y dominio definitivos para Production, y la publicación en Production.

## 2026-09-02 — Gestión interna y entrega final implementadas en Editor

- Se agregaron los campos opcionales `producto_final_url` y `producto_final_nota` a cada servicio solicitado.
- El detalle interno permite registrar una entrega externa HTTPS, su nota y abrir el enlace guardado por servicio.
- `Finalizado` exige producto final válido en interfaz y backend, manteniendo responsable, observaciones y `updatedAt`.
- No se configuraron Resend ni emails, no se creó `/seguimiento` y Production no fue modificada.

## 2026-09-02 — Redesign 310826 core completado en Editor

Se implementó y validó manualmente el core del nuevo flujo público exclusivamente en WeWeb Editor.

- Se activaron las cuatro áreas públicas y los formularios definitivos.
- Se preservó multiselección y la regla `1 servicio o pieza = 1 PED`.
- Se incorporó upload automático aislado por servicio, revisión con adjuntos y acceso interno seguro a archivos privados.
- Se corrigió el progreso a tres pasos y se preservó compatibilidad con pedidos históricos.
- Permanecen pendientes Resend, emails, producto final, comunicaciones, `/seguimiento` y publicación Production.

## 2026-09-01 — Especificación funcional redesign 310826 cerrada

Se cerró la especificación funcional del nuevo flujo público y su gestión futura. Todavía no implementada en WeWeb.

- Se definieron los datos obligatorios del solicitante y el flujo público de tres pasos.
- Se establecieron las cuatro áreas públicas: Diseño gráfico, Cobertura de eventos, Gacetilla y Publicaciones en redes sociales.
- Se detallaron campos, opcionalidad y validaciones para cada pieza o servicio.
- Se confirmó la regla `1 servicio o pieza = 1 PED` con selección múltiple.
- Se delimitaron los estados que generarán correos mediante Resend y la acción independiente para solicitar información faltante.
- Se definió la entrega del producto final mediante URL HTTPS externa, fuera de WeWeb Storage.
- Se documentaron la futura tabla `comunicaciones_pedido` y la futura página `/seguimiento` con validación PED + correo electrónico.
- Se preservaron la arquitectura compatible y todos los datos históricos, sin migraciones destructivas.

## 2026-09-01 — Inicio redesign 310826

Se preservó el snapshot funcional previo en `main` y en el tag `pre-redesign-2026-09-01`.

Se creó la rama `redesign-310826` y se documentó la arquitectura funcional prevista en `docs/redesign-310826.md`. El rediseño todavía no fue implementado en WeWeb.

## 2026-09-01 — Snapshot pre-rediseño

Primer punto de control técnico/documental externo de PEDIDOS.

Representa el último estado funcional anterior a implementar la simplificación definida en `Form modificaciones 310826.pdf`. Registra la arquitectura vigente de un PED por servicio solicitado, sin modificar ni publicar el proyecto WeWeb.
