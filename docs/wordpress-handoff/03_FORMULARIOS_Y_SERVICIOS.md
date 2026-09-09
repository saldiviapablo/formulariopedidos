# 03. Formularios, Validación y Catálogo de Servicios

Este documento detalla exhaustivamente todos los formularios de la plataforma, sus reglas de validación en cliente y servidor, y la especificación de campos de cada tipo de servicio provisto por la Secretaría de Medios.

---

## 1. Catálogo de Servicios y Campos Específicos

El sistema gestiona diversos tipos de servicios de comunicación pública (`tipos_servicio`). En el wizard de `/nueva-solicitud`, al seleccionar un servicio, se despliegan campos adaptados a su naturaleza:

```
+-----------------------------------------------------------------------------------+
|                            CATÁLOGO DE SERVICIOS                                  |
+--------------------------+-----------------------------+--------------------------+
| 1. COBERTURA FOTOGRÁFICA | 2. GACETILLA DE PRENSA      | 3. DISEÑO GRÁFICO        |
| - Fecha y hora evento    | - Titular / Tema propuesto  | - Tipo de pieza (banner, |
| - Ubicación / Dirección  | - Resumen de hechos clave   |   folleto, afiche, etc.) |
| - Contacto en territorio | - Declaraciones destacadas  | - Medidas / Formatos px  |
| - Tipo de entrega fotos  | - Fotos de apoyo adjuntas   | - Texto base y logos     |
+--------------------------+-----------------------------+--------------------------+
| 4. PRODUCCIÓN AUDIOVISUAL| 5. CAMPAÑA / REDES SOCIALES | 6. STREAMING / VIVO      |
| - Guión / Idea central   | - Plataformas de destino    | - Plataforma transmisión |
| - Locación de rodaje     | - Fechas de inicio y fin    | - Requisitos de audio    |
| - Formato (Reels, 16:9)  | - Presupuesto / Objetivos   | - Conectividad en sitio  |
+--------------------------+-----------------------------+--------------------------+
```

### Detalle de Campos por Servicio (`especificacion` JSONB)

#### A. Cobertura Fotográfica (`codigo = 'fotografia'`)
```json
{
  "fecha_evento": "YYYY-MM-DD",
  "hora_inicio": "HH:mm",
  "hora_fin": "HH:mm",
  "lugar_evento": "Salón de Actos Casa de Gobierno",
  "contacto_en_sitio": "Nombre y teléfono del referente",
  "tipo_cobertura": "Protocolar | Artística | Registro general",
  "urgencia_entrega": "Inmediata (redes) | 24 horas | 48 horas"
}
```

#### B. Gacetilla de Prensa (`codigo = 'prensa'`)
```json
{
  "tema_principal": "Texto descriptivo",
  "datos_clave": "Hechos, fechas, cifras",
  "declaraciones_vocero": "Citas textuales del funcionario",
  "difusion_deseada": "Medios locales | Provinciales | Nacionales",
  "fecha_publicacion_sugerida": "YYYY-MM-DD"
}
```

#### C. Diseño Gráfico (`codigo = 'diseno'`)
```json
{
  "tipo_pieza": "Folleto | Afiche | Banner Digital | Vía Pública | Presentación",
  "medidas_o_proporcion": "Ej. 1080x1080px, A4, 2x1 metros",
  "formato_salida": "PDF para imprenta | JPG/PNG Web | Editable",
  "texto_obligatorio": "Texto que debe incluir la pieza",
  "incluye_logos_institucionales": true
}
```

#### D. Producción Audiovisual (`codigo = 'audiovisual'`)
```json
{
  "tipo_video": "Spot institucional | Cobertura resumen | Entrevista | Reel/TikTok",
  "duracion_estimada_segundos": 60,
  "orientacion": "Horizontal (16:9) | Vertical (9:16) | Ambos",
  "requiere_locucion": true,
  "requiere_subtitulos": true
}
```

---

## 2. Inventario de Formularios y Reglas de Validación

### Formulario 1: Nueva Solicitud (`/nueva-solicitud`)

| Campo | Tipo Control | Obligatorio | Regla de Validación Cliente | Regla de Validación Servidor |
| :--- | :--- | :--- | :--- | :--- |
| `solicitante_nombre` | Text Input | Sí | `trim().length >= 2` | `VARCHAR(100) NOT NULL` |
| `solicitante_apellido` | Text Input | Sí | `trim().length >= 2` | `VARCHAR(100) NOT NULL` |
| `solicitante_email` | Email Input | Sí | Regex RFC 5322 estándar | Regex + `VARCHAR(150) NOT NULL` |
| `solicitante_telefono` | Tel Input | Sí | `^[0-9+ -]{6,25}$` | `VARCHAR(50) NOT NULL` |
| `solicitante_cargo` | Text Input | No | Longitud máx 100 | `VARCHAR(100)` |
| `area_id` | Select Dropdown | Sí | UUID válido existente en `areas` | FK UUID válida en `areas.id` |
| `servicios` | Checkbox Group | Sí | `array.length >= 1` | `JSONB / Array con al menos 1 item` |
| `observaciones` | Textarea | No | Longitud máx 2000 | `TEXT` |
| `archivos` | File Uploader | No | Extensiones: pdf, docx, jpg, png, zip. Max 50MB c/u | Validación MIME en storage |

---

### Formulario 2: Solicitud de Acceso Interno (`/solicitar-acceso`)

| Campo | Tipo Control | Obligatorio | Reglas Estrictas |
| :--- | :--- | :--- | :--- |
| `nombre` | Text Input | Sí | Min 2, max 100 caracteres. Trim. |
| `apellido` | Text Input | Sí | Min 2, max 100 caracteres. Trim. |
| `nombre_usuario` | Text Input | Sí | **2 a 30 caracteres**, solo letras, números, guiones o puntos. Normalizado a **minúsculas** (`trim + lowercase`). Unicidad obligatoria. |
| `email` | Email Input | Sí | Email corporativo/institucional. Unicidad obligatoria. |
| `password` | Password Input | Sí | Mínimo 8 caracteres, al menos 1 letra y 1 número. |
| `password_confirm` | Password Input | Sí | Coincidencia idéntica con `password`. |

---

### Formulario 3: Solicitud de Información al Solicitante (`/gestion/pedido/:id/detalle`)

| Campo | Tipo Control | Obligatorio | Regla |
| :--- | :--- | :--- | :--- |
| `motivo` | Textarea | Sí | Mínimo 10 caracteres. Explica qué dato o archivo falta. |
| `archivos_requeridos` | Multi-select / Text | No | Especifica si se solicitan tipos de archivos concretos. |

---

### Formulario 4: Respuesta de Información (`/completar-solicitud`)

| Campo | Tipo Control | Obligatorio | Regla |
| :--- | :--- | :--- | :--- |
| `token` | Hidden / URL param | Sí | Token existente en `solicitudes_informacion` con `estado = 'pendiente'`. |
| `respuesta_texto` | Textarea | Sí | Mínimo 5 caracteres. Aclaración del solicitante. |
| `archivos` | File Uploader | No | Nuevos adjuntos asociados al pedido. |

---

### Formulario 5: Envío de Comunicación por Email (`/gestion/pedido/:id/detalle`)

| Campo | Tipo Control | Obligatorio | Regla |
| :--- | :--- | :--- | :--- |
| `asunto` | Text Input | Sí | Mínimo 3, max 255 caracteres. |
| `mensaje` | Rich Text / Textarea | Sí | Mínimo 5 caracteres. Cuerpo del correo enviado vía n8n/Gmail. |
| `notificar_solicitante` | Checkbox | Sí | Si es true, se envía al email del solicitante. |
