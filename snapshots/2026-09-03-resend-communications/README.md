# Snapshot — comunicaciones transaccionales

Fecha: 2026-09-03
Entorno documentado: WeWeb Editor
Alcance: comunicaciones por correo, trazabilidad interna y seguridad asociada.

Este snapshot describe la implementación sin secretos, credenciales, datos personales, identificadores de proveedor ni datos de QA. No representa una configuración de Production.

## Recursos incluidos

- Tabla `comunicaciones_pedido`.
- View interna de comunicaciones.
- Workflow interno de envío.
- Endpoints de creación, actualización y solicitud de información.
- Bloque interno de comunicaciones.
- Reglas de acceso aplicables.

## Garantías registradas

- Cada comunicación se asocia a un pedido y a un servicio.
- El destinatario se resuelve en backend desde el pedido, no desde el cliente.
- Las operaciones de pedido o de estado no hacen rollback por un error de correo.
- El panel interno conserva el historial de intentos y resultados.
- La información de correo y los adjuntos existentes permanecen bajo las reglas internas y de Storage Private vigentes.
