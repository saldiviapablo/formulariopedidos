# PEDIDOS — cierre funcional previo a UX/UI

Fecha: 2026-09-03
Entorno documentado: WeWeb Editor únicamente.

El sistema funcional está aprobado en Editor: formulario público simplificado con multiselección, un PED por servicio, adjuntos privados, gestión interna, responsables, estados, entrega final, comunicaciones transaccionales y seguimiento público mediante PED y correo.

No hay datos QA, archivos privados, PII, tokens, credenciales ni identificadores de proveedor en este snapshot. Production no fue modificada.

Estado operativo relevante:

- Backend, Auth y Storage de WeWeb instalados.
- Storage de adjuntos: Private.
- Catálogo público: 4 áreas y 7 tipos activos.
- Numeración anual conservada; la secuencia no se reinicia ni reutiliza números.
- `/nueva-solicitud` y `/seguimiento` son públicos; la gestión y las operaciones internas requieren `equipo_interno` o `admin`.
