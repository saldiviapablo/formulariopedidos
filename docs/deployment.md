# Deployment

Este snapshot fue obtenido del `Editor environment` mediante operaciones MCP read-only.

No se ejecutó Publish, no se copió la base de Editor a Production y no se modificó Production. Los archivos JSON no contienen valores de variables de entorno; solamente indican si cada variable aparece configurada por ambiente.

Procedimiento recomendado antes de publicar cambios futuros:

1. Revisar migraciones y security checks.
2. Confirmar variables administradas por WeWeb en el ambiente destino.
3. Verificar Auth, roles y Storage.
4. Ejecutar QA sin reutilizar correlativos.
5. Crear un nuevo snapshot externo.
6. Publicar manualmente sólo con autorización explícita.
