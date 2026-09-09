# 15. Especificación de Endpoints WordPress REST API

Este documento detalla todas las rutas REST API registradas en `/wp-json/pedidos/v1/`, sus callbacks, métodos HTTP, validaciones y permisos.

---

## 1. Tabla Resumen de Rutas REST

| Endpoint | Método | Acceso | Permiso Callback | Función / Controlador |
| :--- | :--- | :--- | :--- | :--- |
| `/pedidos/v1/public/areas` | `GET` | Público | `__return_true` | Obtiene áreas activas ordenadas |
| `/pedidos/v1/public/servicios` | `GET` | Público | `__return_true` | Obtiene catálogo de servicios activos |
| `/pedidos/v1/public/solicitudes` | `POST` | Público | `__return_true` | Crea solicitud multi-servicio (1 por PED) |
| `/pedidos/v1/public/seguimiento` | `POST` | Público | `__return_true` | Consulta estado de pedido por PED+Token/Email |
| `/pedidos/v1/public/completar-info` | `POST` | Público | `__return_true` | Responde requerimiento de información |
| `/pedidos/v1/auth/solicitar-acceso` | `POST` | Público | `__return_true` | Registra usuario con validación 2-30 chars |
| `/pedidos/v1/gestion/pedidos` | `GET` | Autenticado | `current_user_can('gestionar_pedidos')` | Lista pedidos con filtros y paginación |
| `/pedidos/v1/gestion/pedidos/{id}` | `GET` | Autenticado | `current_user_can('gestionar_pedidos')` | Obtiene detalle completo de un pedido |
| `/pedidos/v1/gestion/pedidos/{id}` | `PATCH` | Autenticado | `current_user_can('gestionar_pedidos')` | Actualiza estado, prioridad o notas |
| `/pedidos/v1/gestion/pedidos/{id}/asignar` | `POST` | Autenticado | `current_user_can('gestionar_pedidos')` | Asigna responsable a pedido |
| `/pedidos/v1/gestion/pedidos/{id}/solicitar-info` | `POST` | Autenticado | `current_user_can('gestionar_pedidos')` | Crea solicitud de aclaración |
| `/pedidos/v1/gestion/pedidos/{id}/comunicar` | `POST` | Autenticado | `current_user_can('gestionar_pedidos')` | Envía email institucional vía n8n |
| `/pedidos/v1/gestion/responsables` | `GET` | Autenticado | `current_user_can('gestionar_pedidos')` | Lista responsables (`nombre_usuario`) |
| `/pedidos/v1/admin/usuarios` | `GET` | Admin | `current_user_can('administrar_pedidos')` | Lista usuarios y estados de acceso |
| `/pedidos/v1/admin/usuarios/{id}/gestionar` | `POST` | Admin | `current_user_can('administrar_pedidos')` | Aprueba, rechaza o revoca usuario |
| `/pedidos/v1/admin/usuarios/{id}/nombre-usuario` | `PATCH` | Admin | `current_user_can('administrar_pedidos')` | Modifica `nombre_usuario` (2-30 chars) |

---

## 2. Ejemplo de Implementación del Endpoint Principal (`/public/solicitudes`)

```php
<?php
namespace PedidosMedios\RestApi\Controllers;

use WP_REST_Controller;
use WP_REST_Server;
use WP_REST_Request;
use WP_REST_Response;
use WP_Error;

class PublicPedidosController extends WP_REST_Controller {

    public function register_routes() {
        register_rest_route('pedidos/v1', '/public/solicitudes', [
            'methods' => WP_REST_Server::CREATABLE,
            'callback' => [$this, 'create_solicitud'],
            'permission_callback' => '__return_true',
            'args' => [
                'solicitante' => [
                    'required' => true,
                    'type' => 'object',
                ],
                'servicios' => [
                    'required' => true,
                    'type' => 'array',
                ],
            ]
        ]);
    }

    public function create_solicitud(WP_REST_Request $request) {
        global $wpdb;
        $params = $request->get_json_params();

        // 1. Validaciones
        $solicitante = $params['solicitante'];
        $servicios = $params['servicios'];

        if (empty($servicios) || !is_array($servicios)) {
            return new WP_Error('invalid_services', 'Debe seleccionar al menos un servicio.', ['status' => 400]);
        }

        $wpdb->query('START TRANSACTION');

        try {
            $pedidos_creados = [];
            $current_year = (int)date('Y');

            foreach ($servicios as $srv) {
                // 2. Obtención de secuencia atómica
                $wpdb->query($wpdb->prepare(
                    "UPDATE {$wpdb->prefix}pedidos_secuencias 
                     SET valor = valor + 1 
                     WHERE anio = %d AND nombre = %s",
                    $current_year, 'pedidos_' . $current_year
                ));

                $secuencia = $wpdb->get_row($wpdb->prepare(
                    "SELECT valor, prefijo, anio FROM {$wpdb->prefix}pedidos_secuencias 
                     WHERE anio = %d AND nombre = %s",
                    $current_year, 'pedidos_' . $current_year
                ));

                $numero_pedido = sprintf('%s-%d-%06d', $secuencia->prefijo, $secuencia->anio, $secuencia->valor);
                $token_acceso = bin2hex(random_bytes(32));
                $uuid = wp_generate_uuid4();

                // 3. Inserción de Pedido
                $wpdb->insert("{$wpdb->prefix}pedidos", [
                    'uuid' => $uuid,
                    'numero_pedido' => $numero_pedido,
                    'token_acceso' => $token_acceso,
                    'estado_general' => 'pendiente',
                    'solicitante_nombre' => sanitize_text_field($solicitante['nombre']),
                    'solicitante_apellido' => sanitize_text_field($solicitante['apellido']),
                    'solicitante_email' => sanitize_email($solicitante['email']),
                    'solicitante_telefono' => sanitize_text_field($solicitante['telefono']),
                    'solicitante_cargo' => sanitize_text_field($solicitante['cargo'] ?? ''),
                    'area_id' => (int)$solicitante['area_id'],
                    'observaciones_generales' => sanitize_textarea_field($params['observaciones'] ?? ''),
                ]);

                $pedido_id = $wpdb->insert_id;

                // 4. Inserción de Servicio Solicitado
                $wpdb->insert("{$wpdb->prefix}pedidos_servicios_solicitados", [
                    'uuid' => wp_generate_uuid4(),
                    'pedido_id' => $pedido_id,
                    'tipo_servicio_id' => (int)$srv['tipo_servicio_id'],
                    'estado' => 'pendiente',
                    'especificacion' => wp_json_encode($srv['especificacion'] ?? []),
                ]);

                $pedidos_creados[] = [
                    'numero_pedido' => $numero_pedido,
                    'token_acceso' => $token_acceso,
                    'servicio_id' => $srv['tipo_servicio_id'],
                ];
            }

            $wpdb->query('COMMIT');

            // 5. Disparar Webhook n8n de forma asíncrona
            \PedidosMedios\Services\N8nWebhookService::dispatch('NUEVA_SOLICITUD', [
                'solicitante' => $solicitante,
                'pedidos' => $pedidos_creados,
            ]);

            return new WP_REST_Response([
                'success' => true,
                'message' => 'Solicitud creada con éxito.',
                'pedidos' => $pedidos_creados,
            ], 201);

        } catch (\Exception $e) {
            $wpdb->query('ROLLBACK');
            return new WP_Error('server_error', 'Error al procesar la solicitud: ' . $e->getMessage(), ['status' => 500]);
        }
    }
}
```
