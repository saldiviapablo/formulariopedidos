# 11 - Manejo de Archivos y Storage

> **Estado:** `[WEWEB-VERIFICADO]` / `[CONFIG-VERIFICADO]`  
> **Proyecto WeWeb ID:** `5791a02a-c0b8-49dc-a5b6-afe5fbb53b60`  
> **Fecha de Auditoría:** 2026-09-10

---

## 1. Infraestructura de Storage

El sistema utiliza **WeWeb Private Storage** integrado con la tabla relacional `archivos` en PostgreSQL.
- **Acceso:** Privado por defecto. Ningún archivo es público directamente por URL estática.
- **Descargas:** Se generan URLs prefirmadas (`Signed URLs`) de corta duración (15 minutos) a través del backend workflow `wf_global_download_file`.

---

## 2. Convención de Rutas y Almacenamiento

Los archivos se organizan siguiendo la estructura jerárquica de buckets/directorios:
- **Archivos adjuntos a pedidos:** `pedidos/{pedido_id}/adjuntos/{uuid_archivo}_{nombre_original}`
- **Archivos de respuesta de información:** `solicitudes_info/{solicitud_id}/{uuid_archivo}_{nombre_original}`
- **Assets de marca / Logos:** `assets/brand/GRIS_LOGO_Gob_TDF_AEIAS.png`

---

## 3. Modelo de Metadatos (`archivos`)

| Columna | Tipo | Descripción |
|---|---|---|
| `id` | `uuid` | Identificador único del archivo. |
| `categoria` | `text` | Categoría funcional (`adjunto_solicitud`, `respuesta_info`, `entregable`). |
| `nombre_original` | `text` | Nombre del archivo subido por el usuario (ej: `brief_evento.pdf`). |
| `mime_type` | `text` | Tipo MIME detectado (`application/pdf`, `image/png`, etc.). |
| `size_bytes` | `double precision` | Tamaño en bytes. |
| `storage_path` | `text` | Ruta interna en WeWeb Storage. |
| `servicio` | `uuid` (FK) | Vinculación opcional a un servicio específico. |
| `solicitud_informacion` | `uuid` (FK) | Vinculación opcional a una solicitud de info. |
| `subido_por` | `text` | Origen (`solicitante` o UUID de usuario). |

---

## 4. Evidencia de Verificación
- `[CONFIG-VERIFICADO]`: Backend workflow `api_subir_archivo_solicitud` y tabla `archivos` verificados en schema.
