# Instrucciones para el Candidato

## 👋 ¡Bienvenido!

Gracias por tu interés en la posición de **Desarrollador WordPress Senior**. Esta prueba técnica está diseñada para evaluar tus habilidades en desarrollo backend, frontend, manejo de bases de datos, y planificación de soluciones.

---

## ⏰ Tiempo Estimado

**6-8 horas** de trabajo efectivo

No hay un límite de tiempo estricto, pero te recomendamos no invertir más de 8 horas. La calidad es más importante que la cantidad de funcionalidades.

---

## 📦 Qué Entregar

### 1. Repositorio Git
- URL del repositorio (GitHub, GitLab, o Bitbucket)
- Asegúrate de que el repositorio sea **público**

### 2. Código del Plugin
- Plugin completo y funcional
- Seguir estructura lógica de archivos
- Incluir README con instrucciones de instalación

### 3. Documentación (carpeta `/docs`)
- `ARCHITECTURE.md` - Arquitectura y decisiones técnicas
- `DATABASE.md` - Esquema de base de datos y queries
- `API.md` - Documentación de endpoints REST

### 4. Archivos SQL (carpeta `/database`)
- `schema.sql` - Estructura de tabla personalizada
- `sample-data.sql` - (Opcional) Datos de prueba

---

## 🎯 Prioridades

Si el tiempo es limitado, enfócate en este orden:

### Prioridad Alta (Imprescindible)
1. ✅ Custom Post Type funcionando
2. ✅ Shortcode básico que muestre recursos
3. ✅ Tabla personalizada para tracking
4. ✅ Al menos 2 endpoints REST
5. ✅ Documentación de arquitectura básica

### Prioridad Media (Muy valorado)
6. Filtros AJAX funcionando
7. Panel de administración con estadísticas
8. Documentación completa de API
9. Seguridad implementada (nonces, sanitización)
10. Commits bien organizados

---

## 🚀 Cómo Empezar

### Paso 1: Setup Inicial
```bash
# Clonar tu repositorio
git clone [tu-repo-url]

# Crear estructura básica
mkdir -p education-resources-manager/{includes,admin,public,docs,database}

# Inicializar plugin
touch education-resources-manager/education-resources-manager.php
```

### Paso 2: Planificación
Antes de codificar:
1. Lee el `README.md` principal completamente
2. Revisa `STRUCTURE_EXAMPLE.md` para ideas
3. Boceta tu arquitectura en papel o diagrama
4. Planifica los commits que harás

### Paso 3: Desarrollo
Sugerencia de orden:
1. Archivo principal del plugin
2. Activator con creación de tabla
3. Custom Post Type y Taxonomías
4. Clase de Database con métodos CRUD
5. REST API endpoints básicos
6. Shortcode básico
7. Frontend con filtros AJAX
8. Panel de administración
9. Pulir y refinar

### Paso 4: Documentación
1. Completa ARCHITECTURE.md
2. Documenta DATABASE.md
3. Escribe API.md con ejemplos
4. Actualiza README del plugin

### Paso 5: Testing y Refinamiento
1. Prueba todas las funcionalidades
2. Verifica seguridad básica
3. Asegura que no hay errores PHP
4. Revisa código final

---

## 🛠️ Herramientas Recomendadas

### Ambiente de Desarrollo
- **Local by Flywheel** - Ambiente WordPress local fácil
- **XAMPP/MAMP** - Alternativa clásica

### IDEs/Editores
- **VS Code** con extensiones:
  - PHP Intelephense
  - WordPress Snippets
  - ESLint
- **PhpStorm** - IDE profesional

### Testing
- **Postman** - Para probar endpoints REST
- **Browser DevTools** - Para debugging JavaScript

### Control de Versiones
- **Git** - Control de versiones
- **GitHub Desktop** - Si prefieres GUI

---

## 📝 Mejores Prácticas

### Git
```bash
# Buenos mensajes de commit
✅ "Add Custom Post Type for education resources"
✅ "Sanitize user input in tracking endpoint"
✅ "Add API endpoint documentation"

❌ "update"
❌ "fixes"
❌ "changes"
```

### Código
```php
// ✅ Bueno: Sanitización y validación
$resource_type = sanitize_text_field($_POST['resource_type']);
if (!in_array($resource_type, ['course', 'tutorial', 'ebook', 'video'])) {
    return new WP_Error('invalid_type', 'Tipo de recurso inválido');
}

// ❌ Malo: Sin sanitización
$resource_type = $_POST['resource_type'];
update_post_meta($post_id, '_erm_type', $resource_type);
```

### Seguridad
```php
// ✅ Bueno: Prepared statement
$results = $wpdb->get_results($wpdb->prepare(
    "SELECT * FROM {$table_name} WHERE resource_id = %d",
    $resource_id
));

// ❌ Malo: SQL injection vulnerable
$results = $wpdb->get_results(
    "SELECT * FROM {$table_name} WHERE resource_id = {$resource_id}"
);
```

---

## ❓ Preguntas Frecuentes

### ¿Puedo usar frameworks CSS?
**Sí**, pero preferimos CSS vanilla o capacidades nativas de WordPress.

### ¿Necesito crear un theme?
**No**, solo el plugin. Usa cualquier theme para probar.

### ¿Qué pasa si no termino todo?
**No hay problema**. Es mejor entregar lo que tienes bien hecho que todo a medias. Documenta qué no pudiste completar y cómo lo habrías hecho.

### ¿Puedo usar código de terceros?
**Sí**, pero:
- Atribuye correctamente las fuentes
- Entiende el código que usas
- Explica por qué lo elegiste

### ¿Puedo buscar en Google/StackOverflow?
**¡Por supuesto!** Eso es lo que hacemos todos los días. Lo importante es que entiendas lo que estás implementando.

### ¿Qué versión de WordPress/PHP debo usar?
- **WordPress:** 6.0 o superior
- **PHP:** 7.4 o superior

---

## 🐛 Qué Hacer si Algo Sale Mal

### No puedo activar el plugin
1. Revisa errores PHP en `wp-content/debug.log`
2. Verifica que el archivo principal tenga los headers correctos
3. Asegúrate de que no hay errores de sintaxis

### La tabla no se crea
1. Verifica que `dbDelta()` esté correctamente llamado
2. Revisa el formato del SQL (espacios y sangrías importan)
3. Activa WP_DEBUG y revisa errores

### AJAX no funciona
1. Verifica que el nonce sea correcto
2. Revisa la consola del navegador
3. Asegúrate de usar la acción correcta

### No sé cómo hacer algo
1. Consulta la [documentación oficial de WordPress](https://developer.wordpress.org/)
2. Busca en Google
3. **Si sigues atascado:** Documenta el problema y tu enfoque intentado

---

## 📧 Entrega Final

### Checklist Antes de Enviar
- [ ] Código funciona sin errores
- [ ] Plugin se puede activar correctamente
- [ ] Todos los commits están pusheados
- [ ] README del plugin está completo
- [ ] Documentación en `/docs` está escrita
- [ ] .gitignore apropiado incluido
- [ ] Repositorio es accesible

### Formato de Entrega
Envía un email a: **[jeremias.aragon@globalauthenticity.com]** con:

**Asunto:** Prueba Técnica WordPress - [Tu Nombre]

**Contenido:**
```
Hola,

He completado la prueba técnica para la posición de Desarrollador WordPress Senior.

Repositorio: [URL del repositorio]
Tiempo invertido: [X horas]

Notas adicionales:
[Cualquier cosa que quieras mencionar: limitaciones, features no completadas, etc.]

Saludos,
[Tu nombre]
```

---

## 🎉 Consejos Finales

1. **Lee todo antes de empezar** - Entender el alcance completo te ayudará a priorizar
2. **Commits frecuentes** - Muestra tu proceso de pensamiento
3. **Calidad sobre cantidad** - Mejor hacer menos, pero bien
4. **Documenta decisiones** - Explica el "por qué" de tus elecciones
5. **Prueba antes de entregar** - Asegúrate de que todo funciona
6. **No te estreses** - Esta prueba también evalúa cómo manejas la presión

---

## 🆘 Soporte

Si tienes dudas sobre los **requerimientos** (no sobre cómo implementar):
- Email: [jeremias.aragon@globalauthenticity.com]

---

## 🎯 Lo Que Buscamos

No necesitas ser perfecto. Buscamos evidencia de:

- ✅ Capacidad de planificar antes de codificar
- ✅ Conocimiento sólido de WordPress
- ✅ Código limpio y organizado
- ✅ Conciencia de seguridad
- ✅ Habilidad para documentar
- ✅ Capacidad de aprender y adaptarse

---

¡Buena suerte! Esperamos ver tu solución. 🚀

**El equipo de Global Authenticity**

---

## 🧭 ¿Candidato a Líder de Equipo?

Si te postulas a una posición con **responsabilidad sobre personas** (Tech Lead, Engineering Manager, Líder de Equipo de Desarrollo), debes completar también la capa de liderazgo en [`leadership-track/LEADERSHIP_CHALLENGE.md`](leadership-track/LEADERSHIP_CHALLENGE.md).

- La parte técnica de esta prueba **se mantiene igual** — sigue siendo evaluada con los mismos criterios.
- La capa de liderazgo **no evalúa código**, evalúa cómo lideras: feedback, mentoring, retención, manejo de conflictos, comunicación con negocio, dilemas humanos.
- Suma **~6-8 h asíncronas** + una **sesión live de 60-90 min** con 3 roleplays.
- Entregables separados en `leadership-deliverables/` (no se mezclan con los técnicos).

Si tu rol es puramente IC, ignora la carpeta `leadership-track/`.
