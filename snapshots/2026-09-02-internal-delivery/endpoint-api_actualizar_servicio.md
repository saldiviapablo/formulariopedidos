# api_actualizar_servicio

ID: `ec935c4c-4c71-4d1a-9ea5-fecc8b908312`
Ruta: `PATCH /servicios/actualizar`

## Seguridad

- Requiere autenticación.
- Roles permitidos: `equipo_interno` OR `admin`.

## Campos permitidos

- `estado`
- `responsable_id`
- `observaciones_internas`
- `producto_final_url`
- `producto_final_nota`

## Reglas

- Rechaza campos fuera de la lista permitida.
- El responsable, si existe, debe ser un usuario interno o administrador.
- Un enlace no vacío debe ser HTTPS y no contener espacios.
- `Finalizado` exige un enlace válido del producto final, existente o enviado en la actualización.
- Actualiza `updatedAt` y devuelve únicamente el resultado controlado del servicio.

No contiene secretos, URLs de prueba ni datos de usuarios.
