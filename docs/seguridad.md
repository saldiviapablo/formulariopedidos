# Seguridad

## Acceso

- Solicitante: formulario público, sin login.
- `equipo_interno`: panel, detalle, Views internas y endpoints internos.
- `admin`: mismo acceso interno con capacidad administrativa.
- Storage de PEDIDOS: privado.

## Controles

- Las Views internas requieren `equipo_interno OR admin`.
- Actualización de servicios, listado de responsables y signed URLs requieren roles internos.
- Creación del pedido y carga temporal son públicas, pero validan payload, catálogos, rutas, tipos y metadatos.
- La idempotencia impide repetir un envío exitoso.
- El snapshot no contiene usuarios, sesiones Auth, pedidos, PII, archivos ni secretos.

Los estados de configuración de variables seguras se registran únicamente como booleanos en los JSON redacted.
