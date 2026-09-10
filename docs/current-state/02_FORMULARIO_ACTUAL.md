# 02 - Formulario Actual de Solicitudes (Paso a Paso)

> **Estado:** `[WEWEB-VERIFICADO]` / `[PROD-VERIFICADO]`  
> **Proyecto WeWeb ID:** `5791a02a-c0b8-49dc-a5b6-afe5fbb53b60`  
> **Fecha de Auditoría:** 2026-09-10  
> **Ruta:** `/` (UID: `50ee979b-2ff9-4235-8ea5-6ce664539886`)

---

## 1. Arquitectura del Wizard de 3 Pasos

El formulario público de carga de pedidos opera como un Wizard de 3 pasos gestionado mediante la variable reactiva global `current_step` (valores enteros: 1, 2, 3).

```mermaid
graph LR
    P1[Paso 1: Datos del Solicitante y Seleccion de Areas] -->|Validar y Continuar| P2[Paso 2: Detalle Especifico de Servicios]
    P2 -->|Validar y Continuar| P3[Paso 3: Archivos Adjuntos, Revision y Envio]
    P3 -->|Submit Exitoso| OK[/solicitud-recibida]
    P2 -->|Volver| P1
    P3 -->|Volver| P2
```

---

## 2. Paso 1 — Datos del Solicitante y Selección de Áreas

### 2.1 Campos y Validaciones
| Campo | Variable / Binding | Tipo | Requerido | Validación / Regla de Negocio |
|---|---|---|---|---|
| **Nombre y apellido** | `form_solicitante.nombre_apellido` | Texto | **Sí** | Mínimo 3 caracteres, trimmed |
| **Teléfono** | `form_solicitante.telefono` | Tel / Texto | **Sí** | Formato numérico / celular de contacto |
| **Correo electrónico** | `form_solicitante.correo` | Email | **Sí** | Validación regex RFC 5322 estándar |
| **Área solicitante** | `form_solicitante.area_solicitante` | Select / Texto | **Sí** | Ministerio, Secretaría o Ente de procedencia |
| **¿Qué necesitás solicitar?** | `selected_areas` (Array) | Checkboxes | **Sí (al menos 1)** | Opciones de Áreas disponibles: <br>1. `diseno_grafico` (Diseño gráfico)<br>2. `cobertura_eventos` (Cobertura de eventos)<br>3. `gacetilla` (Gacetilla de prensa)<br>4. `redes_sociales` (Publicaciones en redes sociales) |

### 2.2 Lógica de Transición al Paso 2
El botón **"Siguiente paso"** ejecuta el workflow local `wf_step1_next`:
1. Verifica que los 4 campos de contacto estén completos y válidos.
2. Verifica que `selected_areas.length >= 1`.
3. Si la validación pasa: actualiza `current_step = 2` y hace scroll suave al inicio del formulario.
4. Si falla: activa variables de error `show_errors_step1 = true` y resalta los campos inválidos en rojo.

---

## 3. Paso 2 — Detalle Específico de Servicios por Área

El Paso 2 renderiza dinámicamente acordeones o contenedores condicionales según las áreas marcadas en `selected_areas`.

### 3.1 Área: Diseño Gráfico (`diseno_grafico`)
Renderiza checkboxes para seleccionar los tipos de diseño requeridos:
- **Flyer para Redes Sociales (`flyer_rrss`):**
  - Campos: Formato (Cuadrado 1:1, Vertical 9:16, Story), Texto principal / Copy sugerido, Fecha límite requerida.
- **Invitación Digital (`invitacion_digital`):**
  - Campos: Nombre del evento, Fecha, Hora, Lugar, Modalidad (Presencial / Virtual), Texto o programa.
- **Certificados (`certificado`):**
  - Campos: Nombre de la capacitación/jornada, Firmantes, Listado de destinatarios (texto o archivo posterior).
- **Otros de Diseño (`otros_diseno`):**
  - Campos: Descripción detallada de la pieza requerida, Dimensiones o soporte técnico.

### 3.2 Área: Cobertura de Eventos (`cobertura_eventos`)
- **Tipo de servicio único:** `cobertura_eventos`
- **Campos obligatorios:**
  - `fecha_evento`: Fecha programada del evento (Datepicker).
  - `hora_inicio` / `hora_fin`: Franja horaria de la cobertura.
  - `lugar_evento`: Dirección, edificio o recinto.
  - `ciudad`: Select (`Ushuaia`, `Río Grande`, `Tolhuin`).
  - `autoridades_presentes`: Texto con nombres y cargos de autoridades que asistirán.
  - `requerimientos_cobertura`: Checkboxes (Fotografía, Video, Streaming, Cobertura en vivo).

### 3.3 Área: Gacetilla de Prensa (`gacetilla`)
- **Tipo de servicio único:** `gacetilla`
- **Campos obligatorios:**
  - `contacto_prensa`: Nombre y apellido del vocero o referente técnico.
  - `telefono_contacto`: WhatsApp / celular de contacto directo para el redactor.
  - `informacion_base`: Hecho noticioso, datos clave, declaraciones o borrador.

### 3.4 Área: Redes Sociales (`redes_sociales`)
- **Tipo de servicio único:** `redes_sociales`
- **Campos obligatorios:**
  - `fecha_publicacion_sugerida`: Fecha ideal de publicación.
  - `texto_redes`: Propuesta de texto o mensaje clave.
  - `enlaces_referencia`: Enlaces web opcionales o material de referencia.

---

## 4. Paso 3 — Archivos Adjuntos, Revisión y Envío

### 4.1 Carga de Archivos Adjuntos (`contenido_adicional`)
- Componente de subida de archivos (Dropzone / Multi-file input).
- Restricciones: Máximo 5 archivos, formatos permitidos: `.pdf`, `.png`, `.jpg`, `.jpeg`, `.docx`, `.zip`. Límite de tamaño: 25 MB por archivo.
- Los archivos se almacenan temporalmente en `uploaded_files_state` con sus metadatos (nombre, tamaño, objeto File o blob data).

### 4.2 Resumen de Confirmación
Muestra un panel de solo lectura con:
- Datos del solicitante (Nombre, Correo, Teléfono, Área).
- Lista de servicios y requerimientos configurados.
- Lista de archivos listos para enviar.

### 4.3 Envío Final y Backend Workflow
Al pulsar **"Enviar Solicitud"**:
1. Activa `loading_submission = true` (spinner en botón, deshabilita interfaz).
2. Sube los archivos a WeWeb Private Storage mediante `api_subir_archivo_solicitud`.
3. Ejecuta el Backend Workflow `api_crear_pedido` (`9b40db24-c189-493e-afec-853b05423fcb`) enviando el payload completo:
   ```json
   {
     "nombre_apellido": "Juan Perez",
     "telefono": "2901445566",
     "correo": "juanperez@tierradelfuego.gob.ar",
     "area_solicitante": "Secretaria de Cultura",
     "tipos_pedido": ["diseno_grafico", "gacetilla"],
     "servicios": [
       {
         "area": "diseno_grafico",
         "tipo_servicio": "flyer_rrss",
         "informacion_especifica": { "formato": "1:1", "copy": "..." }
       }
     ],
     "archivos": [ ... ]
   }
   ```
4. El backend:
   - Reserva secuencia atómica (`PED-2026-XXXXXX`).
   - Inserta fila en tabla `pedidos`.
   - Inserta filas correspondientes en tabla `servicios_solicitados`.
   - Asocia registros en `archivos`.
   - Dispara comunicación inicial (n8n native trigger con JWT firmado).
   - Retorna `{ success: true, pedido_visible: "PED-2026-000101", submission_token: "..." }`.
5. El frontend guarda el resultado en `current_submission_result` y redirige inmediatamente a `/solicitud-recibida`.

---

## 5. Evidencia de Verificación
- `[WEWEB-VERIFICADO]`: Todos los bindings y validaciones fueron extraídos de las variables de Pinia en el editor.
- `[PROD-VERIFICADO]`: Flujo de validación en los 3 pasos comprobado en runtime de producción.
