# Seguridad

No existe una View pública de pedidos. La coincidencia PED+correo se resuelve solo en backend. La limitación conocida es que el MCP no expone rate limiting nativo verificable; queda pendiente QA de seguridad. Antes de Production debe configurarse una URL pública definitiva por ambiente.
