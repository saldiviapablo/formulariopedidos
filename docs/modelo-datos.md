# Modelo de datos

Tablas de proyecto:

- `pedidos`: cabecera numerada y datos generales replicados por servicio.
- `servicios_solicitados`: información específica, estado, responsable y observaciones.
- `areas`: catálogo funcional.
- `tipos_servicio`: catálogo relacionado con áreas.
- `archivos`: metadata y referencia privada vinculada a un servicio.
- `secuencias`: contador anual.

Relaciones principales:

- `pedidos 1:N servicios_solicitados`.
- `areas 1:N tipos_servicio`.
- `areas 1:N servicios_solicitados`.
- `tipos_servicio 1:N servicios_solicitados`.
- `servicios_solicitados 1:N archivos`.
- Auth User se relaciona con creador, responsable y usuario que sube archivos.

Para nuevos registros se aplica `1 PED = 1 servicio`; no se migran ni dividen PED históricos.
