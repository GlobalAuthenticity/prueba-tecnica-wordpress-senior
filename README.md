# Prueba Técnica - Desarrollador WordPress Senior

## 📋 Descripción General

Esta prueba técnica evalúa habilidades esenciales de un desarrollador WordPress Senior, incluyendo:
- Arquitectura y planificación de soluciones
- Desarrollo de plugins personalizados
- Gestión de bases de datos y taxonomías
- Frontend con JavaScript moderno
- Buenas prácticas de desarrollo
- Control de versiones con Git

**Tiempo estimado:** 6-8 horas

**Nota:** vas a encontrar las instrucciones detalladas en el archivo "INSTRUCTIONS_FOR_CANDIDATE.md"

---

## 🎯 Objetivo del Proyecto

Desarrollar un **Sistema de Gestión de Recursos Educativos** para un sitio WordPress que permita:

1. Crear y gestionar recursos educativos (cursos, tutoriales, ebooks)
2. Categorizar los recursos
3. Panel de administración personalizado
4. Interfaz de usuario interactiva en el frontend
5. Reportes y estadísticas básicas

---

## 📦 Requerimientos Técnicos

### 1. Plugin Personalizado (Backend)

Crear un plugin llamado `education-resources-manager` que incluya:

#### Custom Post Type: "Recurso Educativo"
- Campos personalizados:
  - Tipo de recurso (curso, tutorial, ebook, video)
  - Nivel de dificultad (principiante, intermedio, avanzado)
  - Duración estimada (en minutos)
  - URL del recurso
  - Instructor/Autor
  - Precio (puede ser gratuito)
  - Estado de publicación (borrador, publicado, archivado)

#### Taxonomías Personalizadas
- **Categorías de recursos** (jerárquica)
- **Etiquetas de habilidades** (no jerárquica)

#### Base de Datos
- Tabla personalizada para registrar visualizaciones/descargas de recursos
- Campos: ID, recurso_id, user_id, fecha, tipo_accion (view/download), IP

#### Panel de Administración
- Página personalizada en el admin de WordPress
- Lista de recursos con filtros por tipo, nivel y categoría
- Estadísticas básicas:
  - Total de recursos por tipo
  - Recursos más visualizados (top 5)
  - Gráfico simple de recursos creados por mes (últimos 6 meses)

### 2. Frontend

#### Shortcode: `[recursos_educativos]`
Crear un shortcode que muestre:
- Listado de recursos con paginación
- Filtros dinámicos (AJAX):
  - Por tipo de recurso
  - Por nivel de dificultad
  - Por categoría
  - Búsqueda por texto
- Tarjetas de recursos mostrando:
  - Título
  - Tipo y nivel
  - Duración
  - Botón "Ver recurso" que registre la visualización

#### JavaScript
- Implementar filtros sin recargar la página
- Validación de formularios
- Efectos de carga (loading states)
- Uso de REST API de WordPress

### 3. REST API

Crear endpoints personalizados:
- `GET /wp-json/erm/v1/resources` - Listar recursos con filtros
- `GET /wp-json/erm/v1/resources/{id}` - Obtener recurso específico
- `POST /wp-json/erm/v1/resources/{id}/track` - Registrar visualización
- `GET /wp-json/erm/v1/stats` - Obtener estadísticas

### 4. Documentación de Planificación

Incluir en la carpeta `/docs`:

#### 1. `ARCHITECTURE.md`
- Diagrama de arquitectura del plugin
- Estructura de archivos y carpetas
- Flujo de datos
- Decisiones técnicas tomadas

#### 2. `DATABASE.md`
- Esquema de la tabla personalizada
- Relaciones con tablas de WordPress
- Queries principales optimizadas

#### 3. `API.md`
- Documentación de cada endpoint
- Parámetros, respuestas y ejemplos
- Manejo de errores

---

## 🏗️ Estructura Esperada del Plugin

```
education-resources-manager/
├── README.md
├── education-resources-manager.php (archivo principal)
├── includes/
│   ├── class-erm-activator.php
│   ├── class-erm-deactivator.php
│   ├── class-erm-post-type.php
│   ├── class-erm-taxonomy.php
│   ├── class-erm-database.php
│   ├── class-erm-admin.php
│   ├── class-erm-rest-api.php
│   └── class-erm-shortcode.php
├── admin/
│   ├── css/
│   │   └── admin-styles.css
│   ├── js/
│   │   └── admin-scripts.js
│   └── views/
│       └── admin-page.php
├── public/
│   ├── css/
│   │   └── public-styles.css
│   └── js/
│       └── public-scripts.js
├── docs/
│   ├── ARCHITECTURE.md
│   ├── DATABASE.md
│   └── API.md
└── assets/
    └── screenshots/
```

---

## ✅ Criterios de Evaluación

### 1. Código (40%)
- Cumplimiento de WordPress Coding Standards
- Arquitectura orientada a objetos
- Uso de namespaces y autoloading (PSR-4 opcional pero valorado)
- Seguridad (sanitización, validación, nonces, prepared statements)
- Hooks y filters bien utilizados

### 2. Funcionalidad (30%)
- Todos los requerimientos implementados
- Sin errores críticos
- Manejo de casos edge
- Experiencia de usuario fluida

### 3. Base de Datos (10%)
- Diseño eficiente
- Queries optimizadas
- Uso correcto de $wpdb
- Índices apropiados

### 4. Frontend/JavaScript (10%)
- Código limpio y modular
- Uso de REST API
- Responsive design
- Accesibilidad básica

### 5. Documentación y Planificación (10%)
- Claridad en la documentación
- Diagramas útiles
- Instrucciones de instalación
- Decisiones bien justificadas

---

## 🚀 Instrucciones de Entrega

### Repositorio Git
1. Crear un repositorio en GitHub/GitLab
2. Commits significativos y bien descritos
3. Incluir `.gitignore` apropiado
4. Branch principal: `main` o `master`

### README del Plugin
Debe incluir:
- Descripción del plugin
- Requisitos (versión mínima de WordPress, PHP)
- Instrucciones de instalación
- Uso del shortcode
- Screenshots (opcional pero valorado)

### Archivos Requeridos
- Código del plugin completo
- Documentación en `/docs`
- Archivo SQL con la estructura de tabla (en `/database/schema.sql`)
- Datos de prueba (opcional, en `/database/sample-data.sql`)

---

## 📝 Notas Importantes

1. **No usar plugins de terceros** para la funcionalidad core (solo para desarrollo si es necesario)
2. **Compatibilidad:** WordPress 6.0+, PHP 7.4+
3. **No es necesario** un diseño elaborado, pero sí funcional y limpio
4. **Seguridad es primordial:** todos los inputs deben ser sanitizados y validados
5. Usar **nonces** para todas las acciones AJAX y formularios

---

## ❓ Preguntas Frecuentes

**¿Puedo usar frameworks CSS como Bootstrap o Tailwind?**
Sí, pero valoramos más el uso de CSS vanilla o las capacidades nativas de WordPress.

**¿Debo crear un theme completo?**
No, solo el plugin. Puedes usar cualquier theme para probar.

**¿Cuántos commits debo hacer?**
Los necesarios para mostrar tu proceso de desarrollo. Commits atómicos y bien descritos son mejores que uno solo gigante.

---

## 📧 Contacto

Si tienes dudas sobre los requerimientos, no dudes en contactarnos.

**¡Buena suerte con la prueba!** 🚀

---

## 🧭 Track de Liderazgo

Si el rol al que te postulas incluye **liderar un equipo de desarrollo** (Tech Lead, Engineering Manager, Líder de Equipo), además de esta prueba técnica deberás completar la capa de liderazgo en [`Evaluación de liderazgo/desafio-de-liderazgo.md`](Evaluación%20de%20liderazgo/desafio-de-liderazgo.md).

Esa capa **no evalúa código**. Evalúa cómo construyes y diriges equipos: feedback, mentoring, manejo de conflictos, retención, comunicación con producto/negocio, dilemas humanos y cultura.

- Ejercicio asíncrono: **6-8 h adicionales** sobre esta prueba.
- Sesión en vivo: **60-90 min** con 3 simulaciones y revisión de dilemas.

Si tu rol es puramente IC (Individual Contributor), ignora la carpeta `Evaluación de liderazgo/`.
