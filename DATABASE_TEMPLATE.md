# Documentación de Base de Datos - Education Resources Manager

## Tabla de Contenidos
1. [Esquema de Datos](#esquema-de-datos)
2. [Tablas Personalizadas](#tablas-personalizadas)
3. [Relaciones](#relaciones)
4. [Queries Principales](#queries-principales)
5. [Índices y Optimización](#índices-y-optimización)

---

## Esquema de Datos

### Diagrama Entidad-Relación

```
┌─────────────────────┐
│     wp_posts        │
│ (education_resource)│
├─────────────────────┤
│ ID (PK)             │
│ post_title          │
│ post_content        │
│ post_status         │
│ post_type           │
└──────────┬──────────┘
           │
           │ 1:N
           │
┌──────────▼──────────────────┐
│     wp_postmeta             │
├─────────────────────────────┤
│ meta_id (PK)                │
│ post_id (FK)                │
│ meta_key                    │
│ meta_value                  │
│                             │
│ Keys usadas:                │
│ - _erm_resource_type        │
│ - _erm_difficulty_level     │
│ - _erm_duration_minutes     │
│ - _erm_resource_url         │
│ - _erm_instructor           │
│ - _erm_price                │
└─────────────────────────────┘

┌──────────────────────┐
│     wp_terms         │
├──────────────────────┤
│ term_id (PK)         │
│ name                 │
│ slug                 │
└──────────┬───────────┘
           │
           │ N:N via wp_term_relationships
           │
┌──────────▼─────────────────┐
│ wp_term_relationships      │
├────────────────────────────┤
│ object_id (FK → wp_posts)  │
│ term_taxonomy_id (FK)      │
└────────────────────────────┘

┌───────────────────────────────┐
│ {prefix}_erm_tracking         │ ← TABLA PERSONALIZADA
├───────────────────────────────┤
│ id (PK, AUTO_INCREMENT)       │
│ resource_id (FK → wp_posts)   │
│ user_id (FK → wp_users)       │
│ action_type (ENUM)            │
│ action_date (DATETIME)        │
│ ip_address (VARCHAR)          │
│ user_agent (TEXT)             │
└───────────────────────────────┘
```

---

## Tablas Personalizadas

### Tabla: `{prefix}_erm_tracking`

**Propósito:** Registrar las interacciones de usuarios con los recursos educativos.

#### Estructura

| Columna        | Tipo           | Nulo | Default | Descripción                                    |
|----------------|----------------|------|---------|------------------------------------------------|
| id             | BIGINT(20)     | NO   | AUTO    | Identificador único                            |
| resource_id    | BIGINT(20)     | NO   | -       | ID del recurso (FK a wp_posts)                 |
| user_id        | BIGINT(20)     | YES  | NULL    | ID del usuario (NULL para visitantes)          |
| action_type    | VARCHAR(20)    | NO   | 'view'  | Tipo de acción: 'view', 'download', 'complete' |
| action_date    | DATETIME       | NO   | CURRENT | Fecha y hora de la acción                      |
| ip_address     | VARCHAR(45)    | YES  | NULL    | IPv4 o IPv6                                    |
| user_agent     | TEXT           | YES  | NULL    | User agent del navegador                       |

#### SQL de Creación

```sql
CREATE TABLE IF NOT EXISTS {prefix}_erm_tracking (
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
    KEY action_date (action_date),
    KEY action_type (action_type)
) {charset_collate};
```

#### Índices

1. **PRIMARY KEY (id)** - Acceso rápido por ID
2. **KEY resource_id** - Consultas de tracking por recurso
3. **KEY user_id** - Historial de usuario
4. **KEY action_date** - Queries ordenadas por fecha
5. **KEY action_type** - Filtrado por tipo de acción

---

## Relaciones

### Custom Post Type: `education_resource`

#### Post Meta Keys

| Meta Key                 | Tipo    | Descripción                              | Valores Permitidos                      |
|--------------------------|---------|------------------------------------------|-----------------------------------------|
| _erm_resource_type       | string  | Tipo de recurso                          | 'course', 'tutorial', 'ebook', 'video'  |
| _erm_difficulty_level    | string  | Nivel de dificultad                      | 'beginner', 'intermediate', 'advanced'  |
| _erm_duration_minutes    | integer | Duración en minutos                      | Número positivo                         |
| _erm_resource_url        | string  | URL del recurso                          | URL válida                              |
| _erm_instructor          | string  | Nombre del instructor/autor              | Texto libre                             |
| _erm_price               | float   | Precio (0 = gratuito)                    | Número decimal positivo                 |

#### Taxonomías

1. **resource_category** (jerárquica)
   - Slug: `resource_category`
   - Tipo: Categorías
   - Ejemplos: "Programación", "Diseño", "Marketing"

2. **skill_tag** (no jerárquica)
   - Slug: `skill_tag`
   - Tipo: Etiquetas
   - Ejemplos: "JavaScript", "WordPress", "SEO"

---

## Queries Principales

### 1. Obtener Recursos con Filtros

**Propósito:** Listar recursos aplicando múltiples filtros

```php
<?php
function erm_get_resources($args = []) {
    $defaults = [
        'post_type' => 'education_resource',
        'posts_per_page' => 10,
        'post_status' => 'publish',
        'orderby' => 'date',
        'order' => 'DESC'
    ];
    
    $query_args = wp_parse_args($args, $defaults);
    
    // Meta query para filtros personalizados
    $meta_query = [];
    
    if (!empty($args['resource_type'])) {
        $meta_query[] = [
            'key' => '_erm_resource_type',
            'value' => sanitize_text_field($args['resource_type']),
            'compare' => '='
        ];
    }
    
    if (!empty($args['difficulty_level'])) {
        $meta_query[] = [
            'key' => '_erm_difficulty_level',
            'value' => sanitize_text_field($args['difficulty_level']),
            'compare' => '='
        ];
    }
    
    if (!empty($meta_query)) {
        $query_args['meta_query'] = $meta_query;
    }
    
    // Tax query para categorías
    if (!empty($args['category'])) {
        $query_args['tax_query'] = [
            [
                'taxonomy' => 'resource_category',
                'field' => 'slug',
                'terms' => sanitize_text_field($args['category'])
            ]
        ];
    }
    
    return new WP_Query($query_args);
}
```

**Índices utilizados:**
- `post_type` en wp_posts
- `meta_key` en wp_postmeta
- `taxonomy` en wp_term_taxonomy

**Complejidad:** O(n log n) con índices apropiados

---

### 2. Top 5 Recursos Más Vistos

**Propósito:** Obtener estadísticas de recursos populares

```php
<?php
global $wpdb;
$table_name = $wpdb->prefix . 'erm_tracking';

$query = $wpdb->prepare("
    SELECT 
        t.resource_id,
        p.post_title,
        COUNT(t.id) as view_count
    FROM {$table_name} t
    INNER JOIN {$wpdb->posts} p ON t.resource_id = p.ID
    WHERE 
        t.action_type = %s
        AND p.post_type = %s
        AND p.post_status = %s
    GROUP BY t.resource_id, p.post_title
    ORDER BY view_count DESC
    LIMIT 5
", 'view', 'education_resource', 'publish');

$results = $wpdb->get_results($query);
```

**Índices utilizados:**
- `action_type` en erm_tracking
- `resource_id` en erm_tracking
- `PRIMARY KEY` en wp_posts

**Complejidad:** O(n log n) para GROUP BY y ORDER BY

---

### 3. Insertar Registro de Tracking

**Propósito:** Registrar una visualización o descarga

```php
<?php
global $wpdb;
$table_name = $wpdb->prefix . 'erm_tracking';

$data = [
    'resource_id' => absint($resource_id),
    'user_id' => get_current_user_id() ?: null,
    'action_type' => sanitize_text_field($action_type),
    'ip_address' => sanitize_text_field($_SERVER['REMOTE_ADDR'] ?? ''),
    'user_agent' => sanitize_text_field($_SERVER['HTTP_USER_AGENT'] ?? '')
];

$format = ['%d', '%d', '%s', '%s', '%s'];

$wpdb->insert($table_name, $data, $format);
```

**Índices utilizados:**
- `AUTO_INCREMENT` en id

**Complejidad:** O(1) para inserción

---

### 4. Estadísticas por Mes

**Propósito:** Obtener recursos creados en los últimos 6 meses

```php
<?php
global $wpdb;

$query = $wpdb->prepare("
    SELECT 
        DATE_FORMAT(post_date, '%%Y-%%m') as month,
        COUNT(*) as total
    FROM {$wpdb->posts}
    WHERE 
        post_type = %s
        AND post_status = %s
        AND post_date >= DATE_SUB(NOW(), INTERVAL 6 MONTH)
    GROUP BY month
    ORDER BY month DESC
", 'education_resource', 'publish');

$results = $wpdb->get_results($query);
```

**Índices utilizados:**
- `post_type` en wp_posts
- `post_status` en wp_posts
- `post_date` en wp_posts

---

## Índices y Optimización

### Índices Existentes en WordPress Core

Aprovechamos estos índices nativos:
- `type_status_date` en wp_posts
- `post_parent` en wp_posts
- `meta_key` en wp_postmeta

### Índices Personalizados en `erm_tracking`

```sql
-- Compuesto para queries frecuentes
CREATE INDEX idx_resource_action_date 
ON {prefix}_erm_tracking (resource_id, action_type, action_date);

-- Para búsquedas por usuario
CREATE INDEX idx_user_date 
ON {prefix}_erm_tracking (user_id, action_date);
```

### Recomendaciones de Optimización

1. **Limitar resultados siempre**
   ```php
   'posts_per_page' => 20, // Nunca -1 en queries públicas
   ```

2. **Usar transients para datos que no cambian frecuentemente**
   ```php
   $cache_key = 'erm_top_resources_' . md5(serialize($args));
   $results = get_transient($cache_key);
   
   if (false === $results) {
       // Ejecutar query
       set_transient($cache_key, $results, HOUR_IN_SECONDS);
   }
   ```

3. **Evitar queries N+1**
   ```php
   // Malo
   foreach ($resources as $resource) {
       $meta = get_post_meta($resource->ID); // Query por cada recurso
   }
   
   // Bueno
   update_meta_cache('post', $resource_ids); // Una sola query
   ```

---

## Migración y Versionado

### Sistema de Versiones

```php
<?php
define('ERM_DB_VERSION', '1.0.0');

function erm_update_database() {
    $installed_version = get_option('erm_db_version', '0.0.0');
    
    if (version_compare($installed_version, ERM_DB_VERSION, '<')) {
        // Ejecutar migraciones
        erm_migrate_to_1_0_0();
        
        update_option('erm_db_version', ERM_DB_VERSION);
    }
}
```

### Script de Migración de Ejemplo

```sql
-- Migración 1.0.0 a 1.1.0
-- Agregar columna session_id

ALTER TABLE {prefix}_erm_tracking 
ADD COLUMN session_id VARCHAR(100) DEFAULT NULL AFTER user_id;

CREATE INDEX idx_session ON {prefix}_erm_tracking (session_id);
```

---

## Mantenimiento

### Limpieza de Datos Antiguos

```php
<?php
// Eliminar tracking de más de 1 año
global $wpdb;
$table_name = $wpdb->prefix . 'erm_tracking';

$wpdb->query("
    DELETE FROM {$table_name}
    WHERE action_date < DATE_SUB(NOW(), INTERVAL 1 YEAR)
");
```

### Respaldo

**Comando mysqldump:**
```bash
mysqldump -u usuario -p database_name {prefix}_erm_tracking > backup.sql
```

**Restauración:**
```bash
mysql -u usuario -p database_name < backup.sql
```

---

## Anexos

### A. Datos de Prueba

```sql
-- Ver archivo: /database/sample-data.sql
```

### B. Queries de Diagnóstico

```sql
-- Tamaño de la tabla
SELECT 
    table_name AS 'Table',
    ROUND(((data_length + index_length) / 1024 / 1024), 2) AS 'Size (MB)'
FROM information_schema.TABLES
WHERE table_name = '{prefix}_erm_tracking';

-- Total de registros por tipo de acción
SELECT action_type, COUNT(*) as total
FROM {prefix}_erm_tracking
GROUP BY action_type;
```

---

## Conclusión

[Resume las decisiones clave de diseño de base de datos y performance]
