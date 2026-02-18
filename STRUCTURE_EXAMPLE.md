# Ejemplo de Estructura del Plugin

Esta es una estructura de referencia sugerida para el plugin. El candidato puede modificarla según su criterio.

```
education-resources-manager/
│
├── education-resources-manager.php          # Archivo principal del plugin
│
├── README.md                                # Documentación del plugin
│
├── uninstall.php                            # Script de desinstalación (opcional)
│
├── includes/                                # Clases principales
│   ├── class-erm-activator.php              # Lógica de activación
│   ├── class-erm-deactivator.php            # Lógica de desactivación
│   ├── class-erm-loader.php                 # Gestión de hooks (opcional)
│   ├── class-erm-post-type.php              # Custom Post Type
│   ├── class-erm-taxonomy.php               # Taxonomías personalizadas
│   ├── class-erm-database.php               # Gestión de tabla personalizada
│   ├── class-erm-admin.php                  # Funcionalidad del admin
│   ├── class-erm-public.php                 # Funcionalidad pública (opcional)
│   ├── class-erm-rest-api.php               # Endpoints REST
│   └── class-erm-shortcode.php              # Shortcode principal
│
├── admin/                                   # Assets y vistas del admin
│   ├── css/
│   │   └── erm-admin.css                    # Estilos del admin
│   ├── js/
│   │   └── erm-admin.js                     # JavaScript del admin
│   ├── views/
│   │   ├── admin-page-main.php              # Vista principal del admin
│   │   └── admin-page-stats.php             # Vista de estadísticas (opcional)
│   └── partials/
│       └── meta-box-resource.php            # Meta boxes (opcional)
│
├── public/                                  # Assets y vistas del frontend
│   ├── css/
│   │   └── erm-public.css                   # Estilos del frontend
│   ├── js/
│   │   └── erm-public.js                    # JavaScript del frontend
│   └── views/
│       └── shortcode-template.php           # Template del shortcode (opcional)
│
├── assets/                                  # Recursos generales
│   ├── images/
│   │   └── icon.png                         # Icono del plugin
│   └── screenshots/
│       ├── screenshot-1.png                 # Capturas para README
│       └── screenshot-2.png
│
├── docs/                                    # Documentación técnica
│   ├── ARCHITECTURE.md                      # Arquitectura del sistema
│   ├── DATABASE.md                          # Documentación de BD
│   └── API.md                               # Documentación de API REST
│
├── database/                                # Scripts de base de datos
│   ├── schema.sql                           # Esquema de tabla personalizada
│   └── sample-data.sql                      # Datos de prueba (opcional)
│
├── languages/                               # Internacionalización (opcional)
│   ├── education-resources-manager.pot      # Template de traducción
│   └── education-resources-manager-es_ES.po # Traducción al español
│
└── tests/                                   # Tests (bonus)
    ├── bootstrap.php
    ├── test-post-type.php
    └── test-rest-api.php
```

## Descripción de Archivos Clave

### education-resources-manager.php
Archivo principal que:
- Define constantes del plugin
- Incluye archivos necesarios
- Registra hooks de activación/desactivación
- Inicializa las clases principales

### includes/class-erm-activator.php
Maneja la activación:
- Crea tabla personalizada
- Registra opciones por defecto
- Flush rewrite rules
- Verifica versión de PHP/WordPress

### includes/class-erm-post-type.php
Registra el Custom Post Type:
- Configuración de labels
- Soporte de features (editor, thumbnail, etc.)
- Capabilities
- REST API exposure

### includes/class-erm-database.php
Gestiona la tabla personalizada:
- Crear/actualizar esquema
- Métodos CRUD
- Queries de estadísticas

### includes/class-erm-rest-api.php
Define endpoints REST:
- Registrar rutas
- Callbacks de endpoints
- Validación de parámetros
- Permisos y autenticación

### includes/class-erm-shortcode.php
Implementa el shortcode:
- Parsear atributos
- Generar HTML
- Encolar assets necesarios

### admin/views/admin-page-main.php
Página principal del admin:
- Listado de recursos
- Filtros
- Estadísticas básicas

## Notas Importantes

1. **Naming Convention**: Prefijo `erm_` o `ERM_` para evitar conflictos
2. **Autoloading**: Considerar usar Composer para autoload PSR-4
3. **Hooks**: Usar `add_action()` y `add_filter()` apropiadamente
4. **Nonces**: Siempre verificar en formularios y AJAX
5. **Sanitización**: Antes de guardar datos
6. **Escaping**: Antes de mostrar datos
7. **Prepared Statements**: Para todas las queries personalizadas

## Ejemplo de Código Inicial

### education-resources-manager.php

```php
<?php
/**
 * Plugin Name: Education Resources Manager
 * Plugin URI: https://example.com/erm
 * Description: Sistema de gestión de recursos educativos
 * Version: 1.0.0
 * Author: Tu Nombre
 * Author URI: https://example.com
 * License: GPL-2.0+
 * License URI: http://www.gnu.org/licenses/gpl-2.0.txt
 * Text Domain: education-resources-manager
 * Domain Path: /languages
 */

// Si este archivo es llamado directamente, abortar.
if (!defined('WPINC')) {
    die;
}

// Definir constantes
define('ERM_VERSION', '1.0.0');
define('ERM_PLUGIN_DIR', plugin_dir_path(__FILE__));
define('ERM_PLUGIN_URL', plugin_dir_url(__FILE__));
define('ERM_PLUGIN_BASENAME', plugin_basename(__FILE__));

// Activación del plugin
function activate_erm() {
    require_once ERM_PLUGIN_DIR . 'includes/class-erm-activator.php';
    ERM_Activator::activate();
}
register_activation_hook(__FILE__, 'activate_erm');

// Desactivación del plugin
function deactivate_erm() {
    require_once ERM_PLUGIN_DIR . 'includes/class-erm-deactivator.php';
    ERM_Deactivator::deactivate();
}
register_deactivation_hook(__FILE__, 'deactivate_erm');

// Cargar las clases principales
require_once ERM_PLUGIN_DIR . 'includes/class-erm-post-type.php';
require_once ERM_PLUGIN_DIR . 'includes/class-erm-taxonomy.php';
require_once ERM_PLUGIN_DIR . 'includes/class-erm-database.php';
require_once ERM_PLUGIN_DIR . 'includes/class-erm-admin.php';
require_once ERM_PLUGIN_DIR . 'includes/class-erm-shortcode.php';
require_once ERM_PLUGIN_DIR . 'includes/class-erm-rest-api.php';

// Inicializar el plugin
function run_erm() {
    // Inicializar clases
    $post_type = new ERM_Post_Type();
    $taxonomy = new ERM_Taxonomy();
    $database = new ERM_Database();
    $admin = new ERM_Admin();
    $shortcode = new ERM_Shortcode();
    $rest_api = new ERM_REST_API();
    
    // Registrar hooks
    add_action('init', [$post_type, 'register']);
    add_action('init', [$taxonomy, 'register']);
    add_action('rest_api_init', [$rest_api, 'register_routes']);
}
add_action('plugins_loaded', 'run_erm');
```

### includes/class-erm-activator.php (ejemplo básico)

```php
<?php
class ERM_Activator {
    
    public static function activate() {
        // Verificar versiones mínimas
        self::check_requirements();
        
        // Crear tabla personalizada
        self::create_tables();
        
        // Registrar opciones por defecto
        self::set_default_options();
        
        // Flush rewrite rules
        flush_rewrite_rules();
    }
    
    private static function check_requirements() {
        global $wp_version;
        
        if (version_compare(PHP_VERSION, '7.4', '<')) {
            wp_die('Este plugin requiere PHP 7.4 o superior');
        }
        
        if (version_compare($wp_version, '6.0', '<')) {
            wp_die('Este plugin requiere WordPress 6.0 o superior');
        }
    }
    
    private static function create_tables() {
        global $wpdb;
        
        $table_name = $wpdb->prefix . 'erm_tracking';
        $charset_collate = $wpdb->get_charset_collate();
        
        $sql = "CREATE TABLE IF NOT EXISTS $table_name (
            id BIGINT(20) UNSIGNED NOT NULL AUTO_INCREMENT,
            resource_id BIGINT(20) UNSIGNED NOT NULL,
            user_id BIGINT(20) UNSIGNED DEFAULT NULL,
            action_type VARCHAR(20) NOT NULL DEFAULT 'view',
            action_date DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
            ip_address VARCHAR(45) DEFAULT NULL,
            user_agent TEXT DEFAULT NULL,
            PRIMARY KEY (id),
            KEY resource_id (resource_id),
            KEY user_id (user_id),
            KEY action_date (action_date)
        ) $charset_collate;";
        
        require_once(ABSPATH . 'wp-admin/includes/upgrade.php');
        dbDelta($sql);
    }
    
    private static function set_default_options() {
        add_option('erm_version', ERM_VERSION);
        add_option('erm_db_version', '1.0.0');
    }
}
```

## Recursos Útiles

- [WordPress Plugin Handbook](https://developer.wordpress.org/plugins/)
- [WordPress Coding Standards](https://developer.wordpress.org/coding-standards/wordpress-coding-standards/)
- [REST API Handbook](https://developer.wordpress.org/rest-api/)
- [Custom Post Types](https://developer.wordpress.org/plugins/post-types/)
