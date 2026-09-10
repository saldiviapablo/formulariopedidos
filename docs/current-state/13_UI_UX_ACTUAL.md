# 13 - Sistema de Diseño, UI/UX y Estilos

> **Estado:** `[WEWEB-VERIFICADO]` / `[PROD-VERIFICADO]`  
> **Proyecto WeWeb ID:** `5791a02a-c0b8-49dc-a5b6-afe5fbb53b60`  
> **Fecha de Auditoría:** 2026-09-10

---

## 1. Identidad Visual y Paleta de Colores

La aplicación implementa la identidad visual institucional del Gobierno de Tierra del Fuego: `[WEWEB-VERIFICADO]`

| Token / Nombre | Valor HEX / RGB | Uso Principal |
|---|---|---|
| **Azul Institucional (Primary)** | `#003366` / `#0B4F8A` | Botones principales, barras de navegación, títulos destacados. |
| **Azul Secundario / Hover** | `#002244` | Hover de botones, estados activos de navegación. |
| **Gris Fondo (Background)** | `#F8F9FA` | Fondo general de páginas y layouts. |
| **Blanco Superficie (Surface)** | `#FFFFFF` | Contenedores de formularios, tarjetas de pedidos y modales. |
| **Borde / Separador** | `#E5E7EB` | Bordes de inputs, tablas y divisiones de tabs. |
| **Texto Principal** | `#1F2937` | Tipografía principal, etiquetas y encabezados. |
| **Texto Secundario / Muted** | `#6B7280` | Textos de ayuda, timestamps y metadatos. |
| **Estado Éxito (Success)** | `#10B981` | Badges de `Finalizado`, alertas de confirmación. |
| **Estado Advertencia (Warning)**| `#F59E0B` | Badges de `Esperando información`, `En revisión`. |
| **Estado Error / Peligro (Danger)**| `#EF4444` | Badges de `Cancelado`, alertas de validación de campos. |
| **Estado Información (Info)** | `#3B82F6` | Badges de `Nuevo`, `Asignado`, `En proceso`. |

---

## 2. Tipografía y Componentes UI `[WEWEB-VERIFICADO]`

- **Familia Tipográfica:** Inter / System UI Font Stack (`font-sans`).
- **Componentes Clave:**
  - **Dropzone:** Borde punteado con feedback visual en drag-over.
  - **Badges de Estado:** Pills redondeadas con color de fondo suave y texto contrastante.
  - **Tabs de Navegación:** Pestañas horizontales con indicador activo azul.
  - **Modales:** Overlays con backdrop oscurecido y foco accesible.

---

## 3. Responsive Design `[PROD-VERIFICADO]`
- **Desktop (>= 1024px):** Layout estructurado en columnas con resumen lateral.
- **Tablet (768px - 1023px):** Kanban con scroll horizontal, formularios en columna única.
- **Mobile (< 768px):** Formularios al 100% de ancho, menú colapsado y botones de toque amplio.

---

## 4. Evidencia de Verificación
- `[WEWEB-VERIFICADO]`: Tokens de Design System extraídos de la configuración de WeWeb.
