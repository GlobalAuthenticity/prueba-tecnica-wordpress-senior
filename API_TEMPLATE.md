# Documentación API REST - Education Resources Manager

## Tabla de Contenidos
1. [Información General](#información-general)
2. [Autenticación](#autenticación)
3. [Endpoints](#endpoints)
4. [Modelos de Datos](#modelos-de-datos)
5. [Códigos de Error](#códigos-de-error)
6. [Ejemplos de Uso](#ejemplos-de-uso)

---

## Información General

### Base URL
```
https://tu-sitio.com/wp-json/erm/v1
```

### Formato de Respuesta
Todas las respuestas están en formato JSON con la siguiente estructura:

**Respuesta Exitosa:**
```json
{
  "success": true,
  "data": { /* datos solicitados */ },
  "message": "Mensaje opcional"
}
```

**Respuesta de Error:**
```json
{
  "success": false,
  "code": "codigo_error",
  "message": "Descripción del error",
  "data": { /* datos adicionales */ }
}
```

### Headers Comunes
```
Content-Type: application/json
Accept: application/json
```

---

## Autenticación

### Endpoints Públicos
Los siguientes endpoints NO requieren autenticación:
- `GET /resources` (listado público)
- `GET /resources/{id}` (detalle de recurso)

### Endpoints Protegidos
Los siguientes endpoints requieren autenticación:
- `POST /resources/{id}/track`
- `GET /stats` (solo para administradores)

### Métodos de Autenticación

#### 1. Nonce (para AJAX desde el frontend)
```javascript
fetch('/wp-json/erm/v1/resources/123/track', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'X-WP-Nonce': ermData.nonce // Provisto por wp_localize_script
  },
  body: JSON.stringify({ action_type: 'view' })
})
```

#### 2. Application Passwords (para uso externo)
```bash
curl -X GET \
  https://tu-sitio.com/wp-json/erm/v1/stats \
  --user "username:application_password"
```

---

## Endpoints

### 1. Listar Recursos

Obtiene un listado de recursos educativos con filtros opcionales.

#### Request
```
GET /wp-json/erm/v1/resources
```

#### Parámetros Query String

| Parámetro      | Tipo    | Requerido | Default | Descripción                                  |
|----------------|---------|-----------|---------|----------------------------------------------|
| page           | integer | No        | 1       | Número de página                             |
| per_page       | integer | No        | 10      | Recursos por página (máx. 100)               |
| search         | string  | No        | -       | Búsqueda por título                          |
| type           | string  | No        | -       | Filtrar por tipo: course, tutorial, ebook, video |
| difficulty     | string  | No        | -       | Filtrar por dificultad: beginner, intermediate, advanced |
| category       | string  | No        | -       | Slug de categoría                            |
| skill          | string  | No        | -       | Slug de habilidad (tag)                      |
| orderby        | string  | No        | date    | Ordenar por: date, title, views              |
| order          | string  | No        | DESC    | Dirección: ASC, DESC                         |

#### Ejemplo de Request
```bash
GET /wp-json/erm/v1/resources?type=course&difficulty=beginner&per_page=5
```

#### Respuesta Exitosa (200 OK)
```json
{
  "success": true,
  "data": {
    "resources": [
      {
        "id": 123,
        "title": "Introducción a WordPress",
        "excerpt": "Aprende los fundamentos...",
        "type": "course",
        "difficulty": "beginner",
        "duration_minutes": 180,
        "url": "https://example.com/curso",
        "instructor": "Juan Pérez",
        "price": 0,
        "categories": [
          {
            "id": 5,
            "name": "Desarrollo Web",
            "slug": "desarrollo-web"
          }
        ],
        "skills": [
          {
            "id": 12,
            "name": "WordPress",
            "slug": "wordpress"
          }
        ],
        "featured_image": "https://example.com/wp-content/uploads/image.jpg",
        "views": 245,
        "permalink": "https://example.com/recursos/intro-wordpress"
      }
    ],
    "pagination": {
      "total": 50,
      "total_pages": 10,
      "current_page": 1,
      "per_page": 5,
      "has_more": true
    }
  }
}
```

#### Códigos de Respuesta
- `200 OK` - Éxito
- `400 Bad Request` - Parámetros inválidos

---

### 2. Obtener Recurso Individual

Obtiene los detalles completos de un recurso específico.

#### Request
```
GET /wp-json/erm/v1/resources/{id}
```

#### Parámetros URL

| Parámetro | Tipo    | Requerido | Descripción            |
|-----------|---------|-----------|------------------------|
| id        | integer | Sí        | ID del recurso         |

#### Ejemplo de Request
```bash
GET /wp-json/erm/v1/resources/123
```

#### Respuesta Exitosa (200 OK)
```json
{
  "success": true,
  "data": {
    "id": 123,
    "title": "Introducción a WordPress",
    "content": "<p>Contenido completo del recurso...</p>",
    "excerpt": "Aprende los fundamentos...",
    "type": "course",
    "difficulty": "beginner",
    "duration_minutes": 180,
    "url": "https://example.com/curso",
    "instructor": "Juan Pérez",
    "price": 0,
    "categories": [
      {
        "id": 5,
        "name": "Desarrollo Web",
        "slug": "desarrollo-web"
      }
    ],
    "skills": [
      {
        "id": 12,
        "name": "WordPress",
        "slug": "wordpress"
      },
      {
        "id": 13,
        "name": "PHP",
        "slug": "php"
      }
    ],
    "featured_image": "https://example.com/wp-content/uploads/image.jpg",
    "views": 245,
    "downloads": 89,
    "permalink": "https://example.com/recursos/intro-wordpress",
    "date_created": "2024-01-15T10:30:00",
    "date_modified": "2024-02-01T14:20:00"
  }
}
```

#### Códigos de Respuesta
- `200 OK` - Éxito
- `404 Not Found` - Recurso no existe

---

### 3. Registrar Tracking

Registra una interacción del usuario con un recurso (visualización, descarga, etc).

#### Request
```
POST /wp-json/erm/v1/resources/{id}/track
```

**Requiere:** Autenticación con nonce

#### Parámetros URL

| Parámetro | Tipo    | Requerido | Descripción    |
|-----------|---------|-----------|----------------|
| id        | integer | Sí        | ID del recurso |

#### Body Parameters

| Parámetro    | Tipo   | Requerido | Descripción                                |
|--------------|--------|-----------|-------------------------------------------|
| action_type  | string | Sí        | Tipo de acción: 'view', 'download', 'complete' |

#### Ejemplo de Request
```bash
POST /wp-json/erm/v1/resources/123/track
Content-Type: application/json
X-WP-Nonce: abc123xyz

{
  "action_type": "view"
}
```

#### Respuesta Exitosa (201 Created)
```json
{
  "success": true,
  "data": {
    "tracking_id": 456,
    "resource_id": 123,
    "action_type": "view",
    "timestamp": "2024-02-13T15:30:00"
  },
  "message": "Acción registrada exitosamente"
}
```

#### Códigos de Respuesta
- `201 Created` - Acción registrada
- `400 Bad Request` - Parámetros inválidos
- `401 Unauthorized` - Falta autenticación
- `404 Not Found` - Recurso no existe

---

### 4. Obtener Estadísticas

Obtiene estadísticas generales del sistema.

#### Request
```
GET /wp-json/erm/v1/stats
```

**Requiere:** Usuario con rol de administrador

#### Parámetros Query String (opcionales)

| Parámetro   | Tipo   | Default | Descripción                      |
|-------------|--------|---------|----------------------------------|
| period      | string | all     | Periodo: 'all', 'month', 'week'  |

#### Ejemplo de Request
```bash
GET /wp-json/erm/v1/stats?period=month
```

#### Respuesta Exitosa (200 OK)
```json
{
  "success": true,
  "data": {
    "summary": {
      "total_resources": 150,
      "total_views": 12453,
      "total_downloads": 3421,
      "total_users_tracked": 892
    },
    "by_type": {
      "course": 45,
      "tutorial": 60,
      "ebook": 30,
      "video": 15
    },
    "by_difficulty": {
      "beginner": 70,
      "intermediate": 55,
      "advanced": 25
    },
    "top_resources": [
      {
        "id": 123,
        "title": "Introducción a WordPress",
        "views": 1245,
        "downloads": 456
      },
      {
        "id": 124,
        "title": "JavaScript Avanzado",
        "views": 1103,
        "downloads": 389
      }
    ],
    "monthly_growth": [
      {
        "month": "2024-02",
        "new_resources": 12,
        "views": 2345,
        "downloads": 678
      },
      {
        "month": "2024-01",
        "new_resources": 15,
        "views": 2100,
        "downloads": 590
      }
    ]
  }
}
```

#### Códigos de Respuesta
- `200 OK` - Éxito
- `401 Unauthorized` - Usuario no autenticado
- `403 Forbidden` - Usuario sin permisos

---

## Modelos de Datos

### Resource Object

```typescript
interface Resource {
  id: number;
  title: string;
  content?: string;          // Solo en detalle individual
  excerpt: string;
  type: 'course' | 'tutorial' | 'ebook' | 'video';
  difficulty: 'beginner' | 'intermediate' | 'advanced';
  duration_minutes: number;
  url: string;
  instructor: string;
  price: number;              // 0 = gratuito
  categories: Category[];
  skills: Skill[];
  featured_image: string | null;
  views: number;
  downloads?: number;         // Solo en detalle individual
  permalink: string;
  date_created: string;       // ISO 8601
  date_modified?: string;     // Solo en detalle individual
}
```

### Category Object

```typescript
interface Category {
  id: number;
  name: string;
  slug: string;
  parent_id?: number;         // Para categorías jerárquicas
}
```

### Skill Object

```typescript
interface Skill {
  id: number;
  name: string;
  slug: string;
}
```

### Pagination Object

```typescript
interface Pagination {
  total: number;              // Total de resultados
  total_pages: number;        // Total de páginas
  current_page: number;       // Página actual
  per_page: number;           // Resultados por página
  has_more: boolean;          // Hay más páginas
}
```

---

## Códigos de Error

### Códigos Estándar HTTP

| Código | Significado             | Cuándo se usa                          |
|--------|-------------------------|----------------------------------------|
| 200    | OK                      | Solicitud exitosa                      |
| 201    | Created                 | Recurso creado exitosamente            |
| 400    | Bad Request             | Parámetros inválidos o faltantes       |
| 401    | Unauthorized            | Autenticación requerida                |
| 403    | Forbidden               | Sin permisos para esta acción          |
| 404    | Not Found               | Recurso no encontrado                  |
| 500    | Internal Server Error   | Error del servidor                     |

### Códigos de Error Personalizados

| Código                    | Mensaje                                | Solución                              |
|---------------------------|----------------------------------------|---------------------------------------|
| `invalid_resource_type`   | Tipo de recurso inválido               | Usar: course, tutorial, ebook, video  |
| `invalid_difficulty`      | Nivel de dificultad inválido           | Usar: beginner, intermediate, advanced|
| `invalid_action_type`     | Tipo de acción inválido                | Usar: view, download, complete        |
| `resource_not_found`      | El recurso no existe                   | Verificar ID del recurso              |
| `invalid_page_number`     | Número de página inválido              | Usar número positivo                  |
| `per_page_exceeds_limit`  | per_page excede el límite máximo       | Máximo 100 recursos por página        |
| `missing_nonce`           | Falta el nonce de seguridad            | Incluir X-WP-Nonce header             |
| `invalid_nonce`           | Nonce inválido o expirado              | Refrescar página y obtener nuevo nonce|

---

## Ejemplos de Uso

### Ejemplo 1: Obtener cursos para principiantes

```javascript
async function getBeginnerCourses() {
  const response = await fetch('/wp-json/erm/v1/resources?type=course&difficulty=beginner&per_page=10');
  const data = await response.json();
  
  if (data.success) {
    console.log('Cursos encontrados:', data.data.resources);
    console.log('Total de páginas:', data.data.pagination.total_pages);
  } else {
    console.error('Error:', data.message);
  }
}
```

### Ejemplo 2: Registrar visualización de recurso

```javascript
async function trackResourceView(resourceId) {
  const response = await fetch(`/wp-json/erm/v1/resources/${resourceId}/track`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'X-WP-Nonce': ermData.nonce // Provisto por wp_localize_script
    },
    body: JSON.stringify({
      action_type: 'view'
    })
  });
  
  const data = await response.json();
  
  if (data.success) {
    console.log('Visualización registrada');
  } else {
    console.error('Error:', data.message);
  }
}
```

### Ejemplo 3: Búsqueda con múltiples filtros

```javascript
async function searchResources(filters) {
  const params = new URLSearchParams({
    search: filters.search || '',
    type: filters.type || '',
    difficulty: filters.difficulty || '',
    category: filters.category || '',
    page: filters.page || 1,
    per_page: 12
  });
  
  const response = await fetch(`/wp-json/erm/v1/resources?${params}`);
  const data = await response.json();
  
  return data.success ? data.data : null;
}

// Uso
const results = await searchResources({
  search: 'wordpress',
  type: 'course',
  difficulty: 'intermediate',
  page: 2
});
```

### Ejemplo 4: Obtener estadísticas (admin)

```javascript
async function getStats(period = 'month') {
  const response = await fetch(`/wp-json/erm/v1/stats?period=${period}`, {
    headers: {
      'X-WP-Nonce': ermData.nonce
    }
  });
  
  const data = await response.json();
  
  if (data.success) {
    console.log('Total recursos:', data.data.summary.total_resources);
    console.log('Top 5:', data.data.top_resources);
  }
}
```

### Ejemplo 5: Implementación con jQuery (compatible con WordPress)

```javascript
jQuery(document).ready(function($) {
  $('#resource-filter').on('change', function() {
    const type = $(this).val();
    
    $.ajax({
      url: '/wp-json/erm/v1/resources',
      method: 'GET',
      data: {
        type: type,
        per_page: 20
      },
      beforeSend: function(xhr) {
        xhr.setRequestHeader('X-WP-Nonce', ermData.nonce);
      },
      success: function(response) {
        if (response.success) {
          renderResources(response.data.resources);
        }
      },
      error: function(xhr) {
        console.error('Error:', xhr.responseJSON.message);
      }
    });
  });
});
```

---

## Rate Limiting

Actualmente no hay límites de tasa implementados, pero se recomienda:
- Máximo 60 solicitudes por minuto para endpoints públicos
- Máximo 120 solicitudes por minuto para usuarios autenticados

---

## Versionado de API

- Versión actual: **v1**
- URL base: `/wp-json/erm/v1`
- Las versiones futuras mantendrán retrocompatibilidad
- Cambios breaking introducirán una nueva versión (v2, v3, etc.)

---

## Soporte y Contacto

Para reportar bugs o solicitar features, contactar a: [jeremias.aragon@globalauthenticity.com]

---

## Changelog

### v1.0.0 (2024-02-13)
- Lanzamiento inicial
- Endpoints básicos de recursos
- Sistema de tracking
- Estadísticas para administradores
