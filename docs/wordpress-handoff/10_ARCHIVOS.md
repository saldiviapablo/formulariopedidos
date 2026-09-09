# 10. Gestión de Archivos, Almacenamiento y Seguridad

Este documento describe la arquitectura de almacenamiento de archivos adjuntos, su modelo de seguridad privada y las especificaciones para su implementación en WordPress.

---

## 1. Arquitectura de Almacenamiento en WeWeb

En WeWeb, los archivos cargados por solicitantes o gestores se guardan en el bucket privado de **WeWeb Storage**.
- Cada archivo genera una fila en la tabla relacional `archivos`:
  - `id`: UUID único.
  - `pedido_id`: UUID del pedido padre.
  - `nombre_original`: Nombre con el que fue subido (ej. `briefing_campana.pdf`).
  - `storage_path`: URI o clave en el almacenamiento seguro.
  - `file_size`: Tamaño en bytes.
  - `mime_type`: Tipo MIME detectado (`application/pdf`, `image/jpeg`, etc.).
  - `privado`: Boolean (`true` por defecto).
  - `subido_por_tipo`: `'solicitante'` | `'equipo'`.

---

## 2. Matriz de Extensiones Permitidas y Restricciones

| Categoría | Extensiones Permitidas | Tamaño Máximo | Validación |
| :--- | :--- | :--- | :--- |
| **Documentos** | `.pdf`, `.doc`, `.docx`, `.xls`, `.xlsx`, `.odt`, `.txt` | 50 MB | Magic Bytes + Extensión |
| **Imágenes** | `.jpg`, `.jpeg`, `.png`, `.webp`, `.svg` | 50 MB | Magic Bytes (verificación de encabezado) |
| **Vectores / Diseño** | `.ai`, `.psd`, `.eps` | 100 MB | Validación MIME |
| **Comprimidos** | `.zip`, `.rar`, `.7z` | 100 MB | Inspección de estructura |
| **Prohibidos** | `.exe`, `.bat`, `.sh`, `.php`, `.js`, `.phtml`, `.html` | **BLOQUEADOS** | Rechazo inmediato en servidor |

---

## 3. Estrategia de Descarga Segura en WordPress

Para replicar el almacenamiento privado de WeWeb en WordPress sin exponer archivos en `/wp-content/uploads/`:

1. **Directorio Protegido**: Almacenar los adjuntos en un directorio no accesible directamente por URL web (ej. `wp-content/uploads/pedidos_privados/` con archivo `.htaccess` conteniendo `Deny from all`, o fuera del `document_root`).
2. **Endpoint de Descarga Controlada**:
   - URL: `/wp-json/pedidos/v1/archivos/{id}/descargar`
   - Parámetros: Token de acceso o Cookie de sesión de usuario autenticado.
   - El servidor valida permisos antes de enviar headers:
     ```php
     header('Content-Type: ' . $archivo->mime_type);
     header('Content-Disposition: attachment; filename="' . $archivo->nombre_original . '"');
     header('Content-Length: ' . $archivo->file_size);
     readfile($ruta_absoluta_protegida);
     exit;
     ```
