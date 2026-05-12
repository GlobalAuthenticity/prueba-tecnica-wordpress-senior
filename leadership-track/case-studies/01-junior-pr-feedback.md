# Case Study 01 — Feedback a un PR de Junior

> **Lo que evaluamos**: tono, balance, jerarquía de issues, pedagogía, tacto.
> **Lo que NO evaluamos**: tu capacidad técnica para encontrar bugs. Aquí los issues ya te los damos.

---

## 🎬 Contexto

Estás en una empresa de tamaño medio. Tu equipo tiene 6 personas. Una de ellas es **Camila**:

- 4 meses en el equipo.
- Su primer trabajo "de verdad" como desarrolladora (antes hizo prácticas y proyectos personales).
- Hace 2 días entregó **su primera feature solo, sin pairing**.
- Está visiblemente orgullosa: lo comentó en standup, le mandó el link del PR a su mentor anterior, posteó algo en su LinkedIn (sin dar detalles, solo *"shipping my first feature"*).
- Su confianza venía remontando después de un mes 2 difícil donde sintió que "no estaba dando la talla".

El PR es para el plugin de gestión de recursos educativos (el de la prueba técnica). Es la implementación del shortcode `[recursos_educativos]` con filtros AJAX.

---

## 🔍 Los 12 issues que tú ya detectaste en el PR

> Estos issues están plantados deliberadamente. No tienes que validarlos técnicamente. Asume que están allí.

1. **🔴 SQL Injection sutil**: una de las consultas concatena un `$_GET['category']` sin pasar por `$wpdb->prepare()`. Está dentro de un método interno que parece "controlado" pero el input viene del frontend.

2. **🔴 Falta de nonce**: el endpoint AJAX para registrar visualizaciones no verifica nonce. Cualquier visitante puede inflar el contador de vistas con un script trivial.

3. **🔴 N+1 query**: para mostrar 12 recursos en una página, hace 1 query principal + 12 queries para obtener el `instructor` de cada uno desde `wp_postmeta`. Cuando el listado crezca, esto colapsa.

4. **🟡 Race condition leve**: la tabla `erm_tracking` se inserta sin transacción cuando se actualiza también un contador en `wp_options`. Posible inconsistencia en alta concurrencia.

5. **🟡 Naming inconsistente**: en el mismo archivo conviven `get_resources()`, `getResource()` y `obtenerCategorias()`. Mezcla snake_case, camelCase y español.

6. **🟡 Magic numbers**: el límite de paginación es `12` hardcoded en 4 lugares distintos del código.

7. **🟡 Dead code**: hay una función `legacy_filter_resources()` que no se usa en ningún sitio, con un comentario `// TODO: borrar después de la migración`.

8. **🟢 `console.log` olvidado**: hay un `console.log('aaaaa', response)` en el JS del shortcode.

9. **🟢 Cero tests**: el PR no incluye ningún test. El equipo no tiene una política estricta de coverage pero los seniors siempre añaden al menos un happy-path test.

10. **🟢 Accesibilidad rota**: los botones de filtro son `<div onclick="...">` sin role ni keyboard support. No se pueden usar con tab.

11. **🟢 Sin i18n**: las cadenas mostradas al usuario están hardcoded en español, sin pasar por `__()` o `_e()`. El proyecto usa i18n en todas las features anteriores.

12. **🟢 Commit message inútil**: el PR tiene un único commit con mensaje `"finished feature"`. Cero contexto, cero descripción de cambios.

**Leyenda**: 🔴 Blocking · 🟡 Importante · 🟢 Sugerencia / educativo

---

## 📝 Tu entregable

Produce dos artefactos:

### Artefacto A — Comentario(s) de review en el PR

> Como lo pondrías literalmente en GitHub/GitLab. Incluye el comentario general del PR y los comentarios inline si los hubiera.

Reglas:
- **Tono apropiado** para Camila (4 meses, primera feature solo).
- **Jerarquía clara**: qué bloquea merge, qué es importante para esta iteración, qué es educativo para próximas.
- **Educativo**: para los 🔴 y 🟡, no solo digas qué está mal — explica brevemente *por qué* y enlaza recursos cuando aplique.
- **Reconocer lo bueno**: el PR tiene cosas bien hechas (asume que sí). Nómbralas. Que no sea cumplido vacío al inicio para "endulzar".
- **No abrumes**: 12 issues de golpe pueden hundir a un junior. Decide qué va al review escrito y qué prefieres hablar en 1:1.

Formato sugerido (puedes adaptarlo):

```markdown
## Review general del PR

[Tu texto aquí]

## Comentarios bloqueantes (🔴)

### Comentario 1 — [archivo o sección]
[Tu comentario aquí]

### Comentario 2 — [archivo o sección]
[Tu comentario aquí]

## Comentarios importantes (🟡)

[...]

## Para próximas iteraciones (🟢)

[...]
```

### Artefacto B — Plan del 1:1 con Camila

Después del review escrito, vas a tener un 1:1 con ella (15-20 min, ad hoc, no el quincenal regular).

Entrega:

- **Por qué** decidiste tener el 1:1 además del review escrito (¿qué cosas NO pusiste en el PR y prefieres hablar en persona?).
- **Cómo lo abres**: la primera frase que dices al sentarte con ella.
- **Qué temas tocas y en qué orden** (lista corta).
- **Qué temas explícitamente NO tocas en este 1:1** (los dejas para después o nunca).
- **Cómo lo cierras**: la frase con la que la mandas de vuelta a su escritorio.

---

## ⚠️ Anti-patrones que estaremos buscando

- Listar los 12 issues sin priorizarlos como si todos pesaran igual.
- Tono condescendiente disfrazado de paciencia (*"tranquila, todos cometemos errores"*).
- Falsa empatía (*"sé que es difícil"*) sin contenido específico.
- Sándwich de feedback estructural (positivo - negativo - positivo) que diluye el mensaje.
- Pasar todos los 🔴 al 1:1 para no "humillarla" en el PR — eso priva al resto del equipo de la trazabilidad del review.
- Ignorar lo del LinkedIn / standup / orgullo, como si fuera información irrelevante.
- Resolver tú los issues (commit directo a su rama) "para no hacerla sentir mal".

---

## 🟢 Señales de un buen entregable

- El review escrito es algo que **Camila podría leer un viernes a las 18:00 y no llegaría a casa destruida**, pero **sí entendiendo claramente que tiene que rehacer cosas críticas**.
- La jerarquía 🔴/🟡/🟢 es coherente con tu criterio (puedes mover alguno de mi clasificación si lo justificas).
- Hay al menos un comentario donde reconoces algo específico y bien hecho.
- El 1:1 toca lo que NO se puede poner por escrito (lo del orgullo, la confianza, el LinkedIn) sin hacerlo incómodo.
- En conjunto, Camila sale del proceso con **claridad técnica** y **confianza intacta o reforzada**.
