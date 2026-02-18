# Guía de Evaluación - Prueba Técnica WordPress Senior

## 📊 Sistema de Puntuación

**Puntuación Total: 100 puntos**

---

## 1. Código y Arquitectura (40 puntos)

### 1.1 Organización y Estructura (10 puntos)

**Excelente (9-10 puntos):**
- Estructura de carpetas lógica y bien organizada
- Separación clara de responsabilidades (MVC o similar)
- Uso de namespaces y autoloading (PSR-4)
- Nombres de archivos y clases descriptivos

**Bueno (6-8 puntos):**
- Estructura organizada pero mejorable
- Separación básica de responsabilidades
- Naming consistente

**Regular (3-5 puntos):**
- Estructura confusa o plana
- Mezcla de responsabilidades
- Inconsistencias en naming

**Deficiente (0-2 puntos):**
- Sin estructura clara
- Todo en un solo archivo
- Nombres poco descriptivos

### 1.2 Calidad del Código (15 puntos)

**Excelente (13-15 puntos):**
- Cumple WordPress Coding Standards
- Código limpio, legible y bien comentado
- DRY (Don't Repeat Yourself)
- Funciones cortas y enfocadas
- Uso apropiado de OOP

**Bueno (9-12 puntos):**
- Mayoría cumple estándares
- Código legible
- Algunas repeticiones menores
- Uso básico de OOP

**Regular (5-8 puntos):**
- Código funcional pero sucio
- Muchas repeticiones
- Comentarios escasos o inexistentes
- OOP mal implementado

**Deficiente (0-4 puntos):**
- Código ilegible
- No sigue estándares
- Programación procedural pura

### 1.3 Seguridad (15 puntos)

**Checklist de Seguridad:**

- [ ] **Sanitización de inputs** (4 puntos)
  - Uso de `sanitize_text_field()`, `sanitize_email()`, etc.
  - Inputs de formularios sanitizados

- [ ] **Validación de datos** (3 puntos)
  - Verificación de tipos de datos
  - Validación de rangos y formatos

- [ ] **Nonces en formularios y AJAX** (3 puntos)
  - Uso correcto de `wp_nonce_field()`
  - Verificación con `wp_verify_nonce()`

- [ ] **Prepared Statements** (3 puntos)
  - Todas las queries usan `$wpdb->prepare()`
  - Sin concatenación directa de SQL

- [ ] **Escaping de outputs** (2 puntos)
  - Uso de `esc_html()`, `esc_attr()`, `esc_url()`
  - Protección contra XSS

**Puntuación de Seguridad:**
- 13-15 puntos: Todos los aspectos cubiertos
- 9-12 puntos: Mayoría cubiertos, algunos faltantes
- 5-8 puntos: Seguridad básica, varios aspectos faltantes
- 0-4 puntos: Seguridad deficiente o inexistente

---

## 2. Funcionalidad (30 puntos)

### 2.1 Backend - Custom Post Type y Taxonomías (8 puntos)

- [ ] CPT registrado correctamente (2 puntos)
- [ ] Todos los campos personalizados implementados (3 puntos)
- [ ] Taxonomías funcionando (2 puntos)
- [ ] Meta boxes o campos bien integrados (1 punto)

### 2.2 Base de Datos Personalizada (7 puntos)

- [ ] Tabla creada correctamente en activación (2 puntos)
- [ ] Estructura de tabla apropiada (2 puntos)
- [ ] Funciones CRUD implementadas (2 puntos)
- [ ] Tracking funciona correctamente (1 punto)

### 2.3 Frontend - Shortcode (8 puntos)

- [ ] Shortcode funcional (2 puntos)
- [ ] Listado de recursos renderiza correctamente (2 puntos)
- [ ] Paginación funciona (1 punto)
- [ ] Diseño responsive básico (1 punto)
- [ ] Botones/enlaces funcionan (1 punto)
- [ ] Loading states implementados (1 punto)

### 2.4 Filtros AJAX (7 puntos)

- [ ] Filtros funcionan sin recargar página (3 puntos)
- [ ] Múltiples filtros funcionan juntos (2 puntos)
- [ ] Búsqueda por texto funciona (1 punto)
- [ ] Manejo de estados vacíos (1 punto)

---

## 3. API REST (15 puntos)

### 3.1 Endpoints Implementados (8 puntos)

- [ ] GET /resources - listado (2 puntos)
- [ ] GET /resources/{id} - detalle (2 puntos)
- [ ] POST /resources/{id}/track (2 puntos)
- [ ] GET /stats (2 puntos)

### 3.2 Calidad de la API (7 puntos)

- [ ] Parámetros correctamente validados (2 puntos)
- [ ] Respuestas en formato JSON estándar (2 puntos)
- [ ] Códigos HTTP apropiados (1 punto)
- [ ] Autenticación implementada donde corresponde (2 puntos)

---

## 4. Panel de Administración (8 puntos)

- [ ] Página de admin accesible (1 punto)
- [ ] Lista de recursos con filtros (2 puntos)
- [ ] Estadísticas básicas mostradas (2 puntos)
- [ ] Top 5 recursos más vistos (1 punto)
- [ ] Gráfico o visualización de datos (2 puntos)

---

## 5. Documentación y Planificación (10 puntos)

### 5.1 ARCHITECTURE.md (4 puntos)

**Excelente (4 puntos):**
- Diagrama de arquitectura claro
- Decisiones técnicas bien justificadas
- Flujo de datos explicado
- Completo y profesional

**Bueno (3 puntos):**
- Documentación completa pero básica
- Decisiones explicadas
- Diagrama presente

**Regular (2 puntos):**
- Documentación incompleta
- Faltan justificaciones
- Diagrama poco claro

**Deficiente (0-1 puntos):**
- Documentación superficial o inexistente

### 5.2 DATABASE.md (3 puntos)

- [ ] Esquema de tabla documentado (1 punto)
- [ ] Queries principales explicadas (1 punto)
- [ ] Índices y optimizaciones documentadas (1 punto)

### 5.3 API.md (3 puntos)

- [ ] Todos los endpoints documentados (1 punto)
- [ ] Ejemplos de uso incluidos (1 punto)
- [ ] Códigos de error listados (1 punto)

---

## 6. Git y Control de Versiones (5 puntos)

### Commits (3 puntos)

**Excelente (3 puntos):**
- Commits atómicos y frecuentes
- Mensajes descriptivos y claros
- Historia de desarrollo lógica

**Bueno (2 puntos):**
- Commits razonables
- Mensajes aceptables
- Historia comprensible

**Deficiente (0-1 puntos):**
- Pocos commits o uno solo
- Mensajes genéricos
- Difícil seguir el desarrollo

### Repositorio (2 puntos)

- [ ] README del plugin completo (1 punto)
- [ ] .gitignore apropiado (0.5 puntos)
- [ ] Estructura de carpetas limpia (0.5 puntos)

---

## 7. Extras Bonus (hasta +20 puntos)

Estos puntos se suman al total pero no son requeridos:

- [ ] **Unit Tests con PHPUnit** (+5 puntos)
- [ ] **WP-CLI commands** (+4 puntos)
- [ ] **Webpack/Gulp para assets** (+3 puntos)
- [ ] **Internacionalización completa** (+3 puntos)
- [ ] **Docker Compose setup** (+3 puntos)
- [ ] **GitHub Actions CI/CD** (+2 puntos)
- [ ] **Caché implementado** (+2 puntos)
- [ ] **Accesibilidad (WCAG)** (+3 puntos)
- [ ] **Screenshots en README** (+1 punto)
- [ ] **Datos de prueba SQL** (+1 punto)

---

## 📋 Plantilla de Evaluación

### Información del Candidato

- **Nombre:** ___________________________
- **Fecha de Evaluación:** _______________
- **Tiempo Invertido (según candidato):** _____ horas
- **Evaluador:** _________________________

### Puntuación por Sección

| Sección                          | Puntos Posibles | Puntos Obtenidos | Notas |
|----------------------------------|-----------------|------------------|-------|
| 1. Código y Arquitectura         | 40              |                  |       |
| 2. Funcionalidad                 | 30              |                  |       |
| 3. API REST                      | 15              |                  |       |
| 4. Panel de Administración       | 8               |                  |       |
| 5. Documentación                 | 10              |                  |       |
| 6. Git y Control de Versiones    | 5               |                  |       |
| **SUBTOTAL**                     | **108**         |                  |       |
| 7. Bonus Extras                  | +20             |                  |       |
| **TOTAL FINAL**                  | **128**         |                  |       |

### Interpretación de Resultados

- **90-108+ puntos (A):** Excelente - Contratar inmediatamente
- **75-89 puntos (B+):** Muy bueno - Candidato sólido
- **60-74 puntos (B):** Bueno - Considerar para segunda entrevista
- **45-59 puntos (C):** Regular - Necesita mejorar
- **0-44 puntos (D/F):** Deficiente - No cumple expectativas

---

## 🔍 Preguntas de Seguimiento Recomendadas

Durante la entrevista técnica, profundizar en:

### Arquitectura
1. ¿Por qué elegiste esta estructura de clases?
2. ¿Consideraste otros enfoques? ¿Cuáles y por qué los descartaste?
3. ¿Cómo escalarías este plugin para 100,000 recursos?

### Seguridad
4. ¿Qué vulnerabilidades comunes previniste?
5. Dame un ejemplo de SQL injection que podrías haber tenido

### Performance
6. ¿Qué optimizaciones implementaste?
7. ¿Cómo manejarías 1000 requests simultáneos al endpoint?
8. ¿Implementarías caché? ¿Dónde y cómo?

### Decisiones Técnicas
9. ¿Por qué usaste [X librería/approach]?
10. Si tuvieras más tiempo, ¿qué mejorarías primero?

---

## 🚩 Red Flags Críticos

Descalificación automática si:

- [ ] Código no funciona en absoluto
- [ ] SQL injection obvio sin prepared statements
- [ ] XSS vulnerabilities evidentes
- [ ] Copia directa de código sin atribución
- [ ] No entrega documentación de planificación

---

## ✅ Checklist de Revisión Rápida (5 minutos)

**Antes de la evaluación detallada, verificar:**

1. [ ] El plugin se puede activar sin errores
2. [ ] El shortcode renderiza algo
3. [ ] Hay al menos 3 commits en Git
4. [ ] Existe documentación en /docs
5. [ ] El código tiene algún tipo de estructura

Si alguno falla, puede indicar entrega incompleta.

---

## 📝 Notas Adicionales del Evaluador

```
[Espacio para comentarios generales, fortalezas destacadas, 
áreas de mejora específicas, y recomendaciones para el equipo]
```

---

## Decisión Final

- [ ] **Contratar** - Excelente candidato
- [ ] **Segunda entrevista** - Necesita clarificación en áreas
- [ ] **Rechazar** - No cumple expectativas
- [ ] **Considerar para posición Junior** - Potencial pero falta experiencia

**Firma del Evaluador:** ___________________ **Fecha:** ___________
