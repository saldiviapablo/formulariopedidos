# 14. Modelo de Datos para WordPress y Script de Instalación

Este documento contiene el script DDL en MySQL/MariaDB compatible con `$wpdb` y `dbDelta()` para crear la base de datos de la plataforma en WordPress.

---

## 1. Script de Creación de Tablas (PHP / dbDelta)

```php
<?php
namespace PedidosMedios\Database;

class Schema {

    public static function create_tables() {
        global $wpdb;
        $charset_collate = $wpdb->get_charset_collate();

        $sql = "
        CREATE TABLE {$wpdb->prefix}pedidos_secuencias (
            id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
            nombre VARCHAR(50) NOT NULL UNIQUE,
            valor BIGINT UNSIGNED NOT NULL DEFAULT 0,
            prefijo VARCHAR(20) NOT NULL DEFAULT 'PED',
            anio INT UNSIGNED NOT NULL,
            updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
            PRIMARY KEY (id)
        ) $charset_collate;

        CREATE TABLE {$wpdb->prefix}pedidos_areas (
            id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
            uuid VARCHAR(36) NOT NULL UNIQUE,
            nombre VARCHAR(100) NOT NULL,
            codigo VARCHAR(50) NOT NULL UNIQUE,
            activo TINYINT(1) NOT NULL DEFAULT 1,
            orden INT NOT NULL DEFAULT 0,
            created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
            PRIMARY KEY (id)
        ) $charset_collate;

        CREATE TABLE {$wpdb->prefix}pedidos_tipos_servicio (
            id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
            uuid VARCHAR(36) NOT NULL UNIQUE,
            codigo VARCHAR(50) NOT NULL UNIQUE,
            nombre VARCHAR(100) NOT NULL,
            descripcion TEXT NULL,
            categoria VARCHAR(50) NOT NULL DEFAULT 'comunicacion',
            campos_requeridos LONGTEXT NULL, -- JSON
            activo TINYINT(1) NOT NULL DEFAULT 1,
            orden INT NOT NULL DEFAULT 0,
            PRIMARY KEY (id)
        ) $charset_collate;

        CREATE TABLE {$wpdb->prefix}pedidos_usuarios_acceso (
            id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
            wp_user_id BIGINT UNSIGNED NOT NULL UNIQUE,
            nombre VARCHAR(100) NOT NULL,
            apellido VARCHAR(100) NOT NULL,
            nombre_usuario VARCHAR(30) NOT NULL UNIQUE,
            estado_acceso VARCHAR(30) NOT NULL DEFAULT 'pendiente', -- pendiente, aprobado, revocado
            solicitado_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
            aprobado_at DATETIME NULL,
            aprobado_por BIGINT UNSIGNED NULL,
            created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
            updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
            PRIMARY KEY (id),
            KEY idx_estado (estado_acceso),
            KEY idx_nombre_usuario (nombre_usuario)
        ) $charset_collate;

        CREATE TABLE {$wpdb->prefix}pedidos (
            id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
            uuid VARCHAR(36) NOT NULL UNIQUE,
            numero_pedido VARCHAR(50) NOT NULL UNIQUE,
            token_acceso VARCHAR(64) NOT NULL UNIQUE,
            estado_general VARCHAR(50) NOT NULL DEFAULT 'pendiente',
            prioridad VARCHAR(20) NOT NULL DEFAULT 'normal',
            solicitante_nombre VARCHAR(100) NOT NULL,
            solicitante_apellido VARCHAR(100) NOT NULL,
            solicitante_email VARCHAR(150) NOT NULL,
            solicitante_telefono VARCHAR(50) NOT NULL,
            solicitante_cargo VARCHAR(100) NULL,
            area_id BIGINT UNSIGNED NOT NULL,
            observaciones_generales TEXT NULL,
            fecha_solicitud DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
            fecha_requerida DATE NULL,
            responsable_id BIGINT UNSIGNED NULL, -- wp_user_id
            created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
            updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
            PRIMARY KEY (id),
            KEY idx_numero (numero_pedido),
            KEY idx_token (token_acceso),
            KEY idx_estado (estado_general),
            KEY idx_solicitante_email (solicitante_email),
            KEY idx_responsable (responsable_id)
        ) $charset_collate;

        CREATE TABLE {$wpdb->prefix}pedidos_servicios_solicitados (
            id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
            uuid VARCHAR(36) NOT NULL UNIQUE,
            pedido_id BIGINT UNSIGNED NOT NULL,
            tipo_servicio_id BIGINT UNSIGNED NOT NULL,
            estado VARCHAR(50) NOT NULL DEFAULT 'pendiente',
            especificacion LONGTEXT NULL, -- JSON
            observaciones TEXT NULL,
            responsable_asignado_id BIGINT UNSIGNED NULL,
            created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
            updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
            PRIMARY KEY (id),
            KEY idx_pedido (pedido_id),
            KEY idx_tipo (tipo_servicio_id)
        ) $charset_collate;

        CREATE TABLE {$wpdb->prefix}pedidos_archivos (
            id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
            uuid VARCHAR(36) NOT NULL UNIQUE,
            pedido_id BIGINT UNSIGNED NOT NULL,
            nombre_original VARCHAR(255) NOT NULL,
            storage_path VARCHAR(500) NOT NULL,
            file_size BIGINT UNSIGNED NOT NULL DEFAULT 0,
            mime_type VARCHAR(100) NOT NULL,
            tipo_archivo VARCHAR(50) NOT NULL DEFAULT 'solicitud',
            privado TINYINT(1) NOT NULL DEFAULT 1,
            subido_por_tipo VARCHAR(20) NOT NULL DEFAULT 'solicitante',
            subido_por_id BIGINT UNSIGNED NULL,
            created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
            PRIMARY KEY (id),
            KEY idx_pedido (pedido_id)
        ) $charset_collate;

        CREATE TABLE {$wpdb->prefix}pedidos_comunicaciones (
            id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
            uuid VARCHAR(36) NOT NULL UNIQUE,
            pedido_id BIGINT UNSIGNED NOT NULL,
            canal VARCHAR(50) NOT NULL DEFAULT 'email',
            tipo_comunicacion VARCHAR(50) NOT NULL DEFAULT 'informativo',
            destinatario_email VARCHAR(150) NOT NULL,
            asunto VARCHAR(255) NOT NULL,
            cuerpo LONGTEXT NOT NULL,
            metadata LONGTEXT NULL, -- JSON
            enviado_por BIGINT UNSIGNED NULL,
            fecha_envio DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
            estado_envio VARCHAR(50) NOT NULL DEFAULT 'enviado',
            PRIMARY KEY (id),
            KEY idx_pedido (pedido_id)
        ) $charset_collate;

        CREATE TABLE {$wpdb->prefix}pedidos_solicitudes_info (
            id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
            uuid VARCHAR(36) NOT NULL UNIQUE,
            pedido_id BIGINT UNSIGNED NOT NULL,
            token VARCHAR(64) NOT NULL UNIQUE,
            motivo TEXT NOT NULL,
            estado VARCHAR(50) NOT NULL DEFAULT 'pendiente',
            solicitada_por BIGINT UNSIGNED NULL,
            solicitada_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
            respondida_at DATETIME NULL,
            respuesta_texto LONGTEXT NULL,
            archivos_respuesta LONGTEXT NULL, -- JSON
            PRIMARY KEY (id),
            KEY idx_token (token),
            KEY idx_pedido (pedido_id)
        ) $charset_collate;
        ";

        require_once(ABSPATH . 'wp-admin/includes/upgrade.php');
        dbDelta($sql);
    }
}
```
