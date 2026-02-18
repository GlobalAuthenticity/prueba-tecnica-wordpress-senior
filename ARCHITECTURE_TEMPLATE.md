# Arquitectura del Sistema - Education Resources Manager

## Tabla de Contenidos
1. [Visión General](#visión-general)
2. [Diagrama de Arquitectura](#diagrama-de-arquitectura)
3. [Estructura de Componentes](#estructura-de-componentes)
4. [Flujo de Datos](#flujo-de-datos)
5. [Decisiones Técnicas](#decisiones-técnicas)

---

## Visión General

### Objetivo
[Describe aquí el objetivo principal del sistema y cómo se integra con WordPress]

### Alcance
[Define qué está incluido y qué está fuera del alcance de esta implementación]

### Stakeholders
- Administradores del sitio
- Creadores de contenido
- Usuarios finales (visitantes)

---

## Diagrama de Arquitectura

### Arquitectura de Alto Nivel

```
┌─────────────────────────────────────────────────────────────┐
│                      WordPress Core                          │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌───────────────────────────────────────────────────────┐  │
│  │         Education Resources Manager Plugin            │  │
│  │                                                         │  │
│  │  ┌──────────────┐  ┌──────────────┐  ┌─────────────┐ │  │
│  │  │   Backend    │  │   Frontend   │  │  REST API   │ │  │
│  │  │              │  │              │  │             │ │  │
│  │  │ - CPT        │  │ - Shortcode  │  │ - Endpoints │ │  │
│  │  │ - Taxonomies │  │ - AJAX       │  │ - Auth      │ │  │
│  │  │ - Admin UI   │  │ - Filters    │  │ - Tracking  │ │  │
│  │  └──────────────┘  └──────────────┘  └─────────────┘ │  │
│  │                                                         │  │
│  │  ┌────────────────────────────────────────────────┐   │  │
│  │  │          Database Layer                        │   │  │
│  │  │  - Custom Tables                               │   │  │
│  │  │  - Post Meta                                   │   │  │
│  │  │  - Term Relationships                          │   │  │
│  │  └────────────────────────────────────────────────┘   │  │
│  └───────────────────────────────────────────────────────┘  │
│                                                               │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
                  ┌──────────────────┐
                  │   MySQL Database │
                  └──────────────────┘
```

### Diagrama de Componentes Detallado

```
[Crea aquí un diagrama mostrando la interacción entre clases]
```

---

## Estructura de Componentes

### 1. Core Components

#### ERM_Activator
**Responsabilidad:** Manejo de activación del plugin
- Crear tablas personalizadas
- Registrar opciones por defecto
- Flush rewrite rules

#### ERM_Deactivator
**Responsabilidad:** Limpieza al desactivar
- Flush rewrite rules
- (Opcional) Limpieza de datos transitorios

#### ERM_Post_Type
**Responsabilidad:** Registro del Custom Post Type
- Configurar labels y capabilities
- Definir supports
- Configurar REST API exposure

#### ERM_Taxonomy
**Responsabilidad:** Registro de taxonomías personalizadas
- Categorías de recursos (jerárquica)
- Etiquetas de habilidades (no jerárquica)

#### ERM_Database
**Responsabilidad:** Gestión de tabla personalizada
- Crear/actualizar esquema
- CRUD operations para tracking
- Queries de estadísticas

### 2. Admin Components

#### ERM_Admin
**Responsabilidad:** Interfaz de administración
- Registrar páginas de menú
- Encolar assets de admin
- Renderizar vistas
- Procesar formularios

### 3. Public Components

#### ERM_Shortcode
**Responsabilidad:** Renderizado del shortcode en frontend
- Parsear atributos
- Generar HTML
- Encolar assets necesarios

### 4. API Components

#### ERM_REST_API
**Responsabilidad:** Endpoints de API REST
- Registrar rutas
- Validación de parámetros
- Autorización
- Respuestas estandarizadas

---

## Flujo de Datos

### Flujo 1: Creación de Recurso

```
[Admin] → [Admin Panel] → [ERM_Post_Type] → [wp_posts table]
                                            → [wp_postmeta table]
                                            → [wp_term_relationships]
```

### Flujo 2: Visualización en Frontend

```
[User] → [Page with Shortcode] → [ERM_Shortcode::render()]
                                       ↓
                                [Query Resources]
                                       ↓
                                [Generate HTML]
                                       ↓
                                [Enqueue JS/CSS]
                                       ↓
                                [Output to Browser]
```

### Flujo 3: Filtrado AJAX

```
[User clicks filter] → [AJAX Request] → [REST API Endpoint]
                                              ↓
                                        [Validate params]
                                              ↓
                                        [Query database]
                                              ↓
                                        [Return JSON]
                                              ↓
                                   [Update DOM with JS]
```

### Flujo 4: Tracking de Visualización

```
[User views resource] → [Track button click]
                              ↓
                        [POST to /track endpoint]
                              ↓
                        [ERM_Database::insert_tracking()]
                              ↓
                        [Custom table insert]
                              ↓
                        [Return success]
```

---

## Decisiones Técnicas

### 1. ¿Por qué Custom Post Type en lugar de tabla personalizada?

**Decisión:** [Explica tu decisión]

**Razones:**
- [Razón 1]
- [Razón 2]
- [Razón 3]

**Alternativas consideradas:**
- [Alternativa 1 y por qué se descartó]

### 2. ¿Por qué tabla personalizada para tracking?

**Decisión:** [Explica tu decisión]

**Razones:**
- [Razón 1]
- [Razón 2]

### 3. REST API vs Admin AJAX

**Decisión:** [Explica tu decisión]

**Razones:**
- [Razón 1]
- [Razón 2]

### 4. Arquitectura de Clases

**Patrón seleccionado:** [Singleton, Factory, etc.]

**Justificación:**
- [Justificación]

### 5. Manejo de Assets

**Estrategia:** [Enqueue condicional, webpack, etc.]

**Justificación:**
- [Justificación]

---

## Seguridad

### Medidas Implementadas

1. **Sanitización de Inputs**
   - [Describe cómo se sanitizan los inputs]

2. **Validación de Datos**
   - [Describe la estrategia de validación]

3. **Nonces**
   - [Explica el uso de nonces en formularios y AJAX]

4. **Capabilities**
   - [Lista los capability checks implementados]

5. **Prepared Statements**
   - [Describe el uso de $wpdb->prepare()]

6. **Escaping de Outputs**
   - [Explica las funciones de escape utilizadas]

---

## Performance

### Optimizaciones Implementadas

1. **Queries de Base de Datos**
   - [Describe optimizaciones específicas]
   - Uso de índices
   - Limitación de resultados

2. **Caching**
   - [Si aplica, describe la estrategia de caché]
   - Transients API
   - Object caching

3. **Asset Loading**
   - [Cómo se minimizan las solicitudes]
   - Enqueue condicional
   - Defer/async cuando sea apropiado

---

## Escalabilidad

### Consideraciones

1. **Volumen de Datos**
   - [Cómo maneja el plugin grandes cantidades de recursos]

2. **Tráfico Concurrente**
   - [Estrategias para múltiples usuarios simultáneos]

3. **Crecimiento Futuro**
   - [Puntos de extensión para nuevas funcionalidades]

---

## Testing

### Estrategia de Pruebas

1. **Unit Tests**
   - [Componentes a testear]
   - Framework: PHPUnit

2. **Integration Tests**
   - [Flujos end-to-end a testear]

3. **Manual Testing Checklist**
   - [ ] Crear recurso desde admin
   - [ ] Editar recurso existente
   - [ ] Ver listado con shortcode
   - [ ] Aplicar filtros
   - [ ] Registrar tracking
   - [ ] Ver estadísticas
   - [ ] Probar REST API endpoints

---

## Dependencias

### WordPress Core
- Versión mínima: 6.0
- Features utilizadas: REST API, Custom Post Types, Taxonomies

### PHP
- Versión mínima: 7.4
- Extensiones requeridas: mysqli, json

### Librerías Externas (si aplica)
- [Lista cualquier librería de terceros]

---

## Roadmap Futuro

### Fase 2 (Mejoras Potenciales)
- [ ] Sistema de valoraciones
- [ ] Comentarios en recursos
- [ ] Integración con LMS
- [ ] Certificados de completación
- [ ] Gamificación

### Fase 3 (Optimizaciones)
- [ ] Elasticsearch para búsqueda avanzada
- [ ] CDN para assets
- [ ] Progressive Web App

---

## Mantenimiento

### Actualización de Base de Datos
[Describe el proceso de versionado del esquema de BD]

### Backward Compatibility
[Explica cómo se mantiene compatibilidad con versiones anteriores]

### Logging
[Estrategia de logs para debugging]

---

## Conclusión

[Resume las decisiones clave y la visión general del sistema]
