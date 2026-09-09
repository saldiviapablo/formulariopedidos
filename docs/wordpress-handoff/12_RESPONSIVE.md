# 12. Especificaciones de Diseño Responsive y Adaptabilidad

Este documento define las reglas de comportamiento responsive, breakpoints y adaptaciones de interfaz para dispositivos móviles, tablets y escritorios.

---

## 1. Breakpoints Estándar

| Dispositivo | Prefijo CSS | Ancho Mínimo | Layout Comportamiento |
| :--- | :--- | :--- | :--- |
| **Mobile Vertical** | Base (`< 640px`) | `320px` | 1 columna vertical, menús hamburguesa, inputs 100% width |
| **Tablet / Mobile H.**| `sm:` (`>= 640px`) | `640px` | 2 columnas en grids de contacto, tarjetas lado a lado |
| **Desktop / Laptop** | `md:` (`>= 1024px`) | `1024px` | Bandeja tabular con scroll horizontal o columnas completas |
| **Wide Screen** | `lg:` (`>= 1280px`) | `1280px` | Layouts centrados con max-width de 1200px |

---

## 2. Adaptaciones Específicas por Pantalla

### 2.1. Wizard de Nueva Solicitud (`/nueva-solicitud`)
- **Desktop**:
  - Stepper horizontal con números circulares, títulos y conectores de línea.
  - Catálogo de servicios en grid de 3 columnas (`grid-cols-3`).
- **Mobile**:
  - Stepper compacto (ej. "Paso 2 de 3: Servicios").
  - Catálogo de servicios en 1 columna vertical con acordeones desplegables al seleccionar.
  - Botones de navegación ("Atrás" / "Siguiente") fijados en la parte inferior o apilados a ancho completo (`w-full`).

### 2.2. Bandeja de Gestión (`/gestion`)
- **Desktop**:
  - Tabla HTML completa con 9 columnas, selects en celda y paginador inferior.
- **Mobile**:
  - La tabla se transforma en **Card List (Lista de Tarjetas)**:
    - Cada tarjeta muestra el Número PED destacado, badge de estado, solicitante y un botón primario "Ver Detalle".
    - Los filtros se agrupan dentro de un panel colapsable / modal de filtros ("Filtrar pedidos").

### 2.3. Detalle de Pedido (`/gestion/pedido/:id/detalle`)
- **Desktop**:
  - Layout de 2 columnas: Columna izquierda (70%) con pestañas de contenido; Columna derecha (30%) con resumen de estado, responsable asignado y bitácora rápida.
- **Mobile**:
  - 1 columna vertical con selector tab de navegación horizontal con scroll táctil (`overflow-x-auto`).
