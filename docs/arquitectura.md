# Arquitectura

## Plataforma

WeWeb es el núcleo full-stack del sistema:

- WeWeb Database para datos y catálogos.
- WeWeb Backend para validación, numeración, idempotencia y persistencia atómica.
- WeWeb Auth para la gestión interna.
- WeWeb Storage Private para adjuntos.
- WeWeb Interface para el formulario público y el panel interno.

## Regla vigente

Cada servicio nuevo genera un PED independiente. Un mismo envío puede contener múltiples áreas y múltiples servicios, pero el Backend crea un registro de `pedidos` por cada elemento de `payload.servicios`.

La relación física permanece `pedidos 1:N servicios_solicitados` para mantener compatibilidad con pedidos históricos. Los PED nuevos tienen cardinalidad efectiva 1:1.

## Componentes funcionales

- `/nueva-solicitud`: formulario público sin login.
- `/gestion`: listado interno, una fila por servicio.
- `/gestion/pedido/{pedidoid}/detalle`: detalle interno.
- Numeración anual: `PED-AAAA-NNNNNN`.
- Idempotencia: UUID base `submissionToken` y token derivado por servicio.
- Archivos: `archivo → servicio → PED`.
