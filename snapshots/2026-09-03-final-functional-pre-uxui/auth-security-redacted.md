# Auth y seguridad

- WeWeb Auth está habilitado.
- Roles definidos: `solicitante`, `equipo_interno`, `admin`.
- Las operaciones internas de actualización, responsables, signed URLs y solicitud de información exigen `equipo_interno` o `admin`.
- El formulario y el seguimiento son públicos por diseño.
- La consulta de seguimiento requiere coincidencia de PED y correo y devuelve solo información pública mínima.
- No se incluyen usuarios, correos, contraseñas, sesiones, secretos ni identificadores de usuarios.
