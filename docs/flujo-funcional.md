# Flujo funcional

## Solicitud pública

1. El solicitante ingresa sus datos generales.
2. Selecciona una o más áreas.
3. Selecciona una o más piezas o servicios.
4. Completa los formularios específicos y adjunta archivos cuando corresponde.
5. Revisa la solicitud y confirma el envío.
6. `wf_submit_request` construye un payload ordenado y llama a `api_crear_pedido_completo`.
7. El Backend reserva un correlativo por servicio y persiste todo atómicamente.
8. La pantalla final muestra una línea `Área — Servicio — PED` por servicio.

## Gestión interna

Los roles `equipo_interno` y `admin` acceden a `/gestion`, filtran servicios, abren el detalle por `pedido.id` y actualizan estado, responsable y observaciones internas.

Los archivos permanecen privados y se consultan mediante URLs firmadas temporales.
