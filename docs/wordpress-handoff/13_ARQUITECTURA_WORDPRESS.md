# 13. Arquitectura del Plugin de WordPress

Este documento detalla la arquitectura técnica modular recomendada para reimplementar la plataforma en WordPress.org mediante un plugin a medida orientado a objetos (OOP) y estándar PSR-4.

---

## 1. Estructura de Directorios del Plugin (`pedidos-medios`)

```
wp-content/plugins/pedidos-medios/
├── pedidos-medios.php              # Archivo principal / Bootstrap del plugin
├── composer.json                   # Autoload PSR-4
├── uninstall.php                   # Limpieza segura en desinstalación
├── includes/
│   ├── Core/
│   │   ├── Plugin.php              # Inicializador y contenedor de servicios
│   │   ├── Activator.php           # Creación de tablas e instalación de roles
│   │   ├── Deactivator.php         # Tareas de desactivación
│   │   └── Autoloader.php          # Autoloading de clases
│   ├── Database/
│   │   ├── Schema.php              # Definición DDL y migraciones dbDelta
│   │   ├── QueryBuilder.php        # Abstracción segura sobre $wpdb
│   │   └── Repositories/           # Clases de acceso a datos
│   │       ├── PedidoRepository.php
│   │       ├── ServicioRepository.php
│   │       ├── ArchivoRepository.php
│   │       ├── SolicitudInfoRepository.php
│   │       └── UsuarioAccesoRepository.php
│   ├── Services/
│   │   ├── SequenceService.php     # Generador atómico de números PED-YYYY-NNNNNN
│   │   ├── N8nWebhookService.php   # Despachador de eventos a n8n
│   │   ├── StorageService.php      # Manejo de subida y descarga de archivos privados
│   │   └── AuthService.php         # Gestión de roles, estados y nombre_usuario
│   ├── RestApi/
│   │   ├── ApiController.php       # Registro de rutas /wp-json/pedidos/v1/
│   │   ├── Controllers/
│   │   │   ├── PublicPedidosController.php
│   │   │   ├── InternalGestionController.php
│   │   │   ├── SolicitudesInfoController.php
│   │   │   └── AdminUsuariosController.php
│   │   └── Middleware/
│   │       ├── AuthMiddleware.php  # Verificación de JWT / Nonce / Capacidades
│   │       └── RateLimiter.php     # Protección contra abusos
│   └── Admin/
│       ├── AdminMenu.php           # Registro de menús en el escritorio de WP
│       └── SettingsPage.php        # Configuración de URLs de n8n, storage y tokens
├── templates/
│   ├── public/
│   │   ├── home.php                # Shortcode / Plantilla Home
│   │   ├── nueva-solicitud.php     # Wizard 3 Pasos
│   │   ├── seguimiento.php         # Dashboard de consulta pública
│   │   └── completar-solicitud.php # Formulario de respuesta con token
│   └── internal/
│       ├── gestion-bandeja.php     # Bandeja de pedidos
│       ├── pedido-detalle.php      # Vista detallada con pestañas
│       └── admin-usuarios.php      # Gestión de usuarios internos
└── assets/
    ├── css/
    │   ├── pedidos-public.css      # Estilos compilados (Tailwind / Custom)
    │   └── pedidos-admin.css
    └── js/
        ├── wizard-solicitud.js     # Lógica cliente del wizard (Alpine.js / Vanilla JS)
        ├── seguimiento.js
        └── gestion-app.js
```

---

## 2. Decisiones de Arquitectura: Tablas a Medida vs CPT (Custom Post Types)

| Criterio | Custom Post Types (`wp_posts` + `wp_postmeta`) | Tablas Personalizadas (`wp_pedidos_*`) | Decisión para este Proyecto |
| :--- | :--- | :--- | :--- |
| **Rendimiento con Alto Volumen** | Lento (múltiples JOINs contra postmeta) | **Óptimo (Consultas indexadas < 10ms)** | **Tablas Personalizadas** |
| **Integridad Relacional (FKs)** | No soportado nativamente por WP | **Soportado (InnoDB Foreign Keys / Índices)** | **Tablas Personalizadas** |
| **Concurrencia y Secuencias Atómicas** | Inseguro para numeración correlativa estricta | **Garantizado (`SELECT ... FOR UPDATE`)** | **Tablas Personalizadas** |
| **Independencia de Schema** | Depende de la estructura general de WP | **Totalmente desacoplado y portable** | **Tablas Personalizadas** |

> **Conclusión de Arquitectura**: Se utilizarán tablas personalizadas de base de datos MySQL/MariaDB bajo el prefijo del sitio (`{$wpdb->prefix}pedidos_*`), permitiendo máxima velocidad, integridad transaccional y coincidencia 1 a 1 con el schema PostgreSQL de WeWeb.
