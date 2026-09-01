# PEDIDOS

PEDIDOS es el sistema de la Secretaría de Medios para recibir solicitudes públicas de piezas y servicios, numerarlas y gestionarlas internamente.

La plataforma full-stack principal es WeWeb: Database, Backend, Auth, Interface y Storage Private. El formulario público permite seleccionar múltiples áreas y servicios; el Backend crea un pedido independiente por cada servicio solicitado, con numeración `PED-AAAA-NNNNNN`, idempotencia por `submissionToken` y archivos vinculados al servicio correspondiente.

El estado documentado incluye el formulario público `/nueva-solicitud`, el panel protegido `/gestion`, el detalle interno, tres roles y seis tablas de proyecto.

> Este repositorio es un snapshot técnico/documental generado desde WeWeb mediante MCP. No contiene el código exportado de WeWeb porque el proyecto actualmente utiliza un plan que no incluye Code Export/GitHub Sync.

No contiene pedidos reales, usuarios Auth, archivos privados, credenciales ni valores de variables seguras. Su finalidad es mantener puntos de control externos y auditables antes de cambios funcionales importantes.
