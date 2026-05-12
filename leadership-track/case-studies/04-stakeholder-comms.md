# Case Study 04 — Comunicación a Stakeholders

> **Lo que evaluamos**: traducción técnico → negocio, framing por impacto, capacidad de "no" sin enfrentamiento, anticipación de objeciones.
> **Lo que NO evaluamos**: profundidad técnica de la deuda. La descripción técnica del problema ya te la damos; tu trabajo es traducirla.

---

## 🎬 Contexto

Eres líder del equipo del plugin de gestión de recursos educativos. Tu empresa vende SaaS B2B a universidades y centros de formación. **75 clientes activos**. Tu equipo es 5 personas.

### Lo técnico (te lo damos resumido, no profundices)

Heredaste el plugin con **deuda técnica significativa**. Lo más crítico:

- El sistema de tracking de visualizaciones (tabla `erm_tracking`) no fue diseñado para escala. Cada vista hace un INSERT directo y un UPDATE a un contador en `wp_options`. Bajo carga genera locks.
- La paginación en el listado de recursos hace queries N+1 con `wp_postmeta`. Con clientes que tienen >5.000 recursos, cargar la página tarda 8-14 segundos.
- No hay caché en endpoints REST. Cada request golpea BD.
- El admin de WordPress muere si un cliente carga más de 1.000 recursos (timeout de PHP).

### El impacto que ESO está causando ahora

- **2 clientes grandes** (universidades, ~40% del MRR cada una) reportaron lentitud severa el último mes.
- **18 tickets de soporte al mes** sobre performance. Crecen un 12% mensual.
- **Soporte se está quemando**: el último 1:1 que tuvo el lead de soporte con tu jefa terminó con *"si no se arregla, voy a tener que dejar de cubrirles"*.
- **Una cuenta clave** está renegociando contrato a la baja porque *"el producto no escala con nuestro tamaño"*.
- **2 bugs P1 en producción este trimestre** (cada uno con ~3h de outage parcial). Los tres tuvieron como factor contribuyente la deuda técnica descrita.

### Lo que necesitas

**8 semanas del equipo completo** (40 person-weeks) para resolver lo crítico:
- Refactor del módulo de tracking con buffering async + batch inserts.
- Rediseño de queries con JOINs apropiados + caché objeto.
- Cache layer para REST API (CDN + memcached).
- Paginación adecuada en admin.

### El terreno político

- **Carla, PM senior** (con quien priorizas el roadmap): piensa que esto es *"perfeccionismo de ingenieros"*. Quiere meter 3 features nuevas este trimestre porque *"ya las prometimos a 4 clientes"*. Llama al refactor *"limpieza"*. No tiene mala intención, no entiende el alcance.

- **Diego, CEO**: técnico de formación pero hace 6 años no toca código. Escuchó la palabra *"refactor"* en su anterior empresa y le costó **8 meses de retraso en una feature crítica**. Desde entonces, la palabra le activa todas las alarmas. Te dijo el mes pasado en pasillo: *"chicos, no me hagan otro refactor por favor".*

- **Tu jefa de ingeniería**: aliada pero "que no me lo escalen". Quiere que tú lo negocies con Carla y Diego. Te apoyará si llegas alineado, no si llegas con conflicto.

- **Soporte**: tu aliado de facto. Tienen los datos.

---

## 📝 Tu entregable

Produce **tres artefactos**, en este orden de audiencia.

### Artefacto A — Email a Carla (PM)

Email que vas a mandarle hoy. Ella es tu primera parada porque sin su acuerdo, el roadmap no se mueve.

Reglas:
- **Asunto** que no diga "refactor".
- **Primera línea** que no la ponga a la defensiva.
- **Datos concretos**: tickets, MRR en riesgo, cliente renegociando.
- **Trade-offs explícitos**: si te da 8 semanas, qué pierde ella (no escondas el costo).
- **Alternativas**: 2-3 caminos con sus pros/contras (ej. full 8 sem vs. parcial 4 sem + features paralelas vs. solo 1 cliente piloto vs. nada y aceptamos riesgo).
- **Pregunta de cierre** que invite a reunión, no que pida aprobación por email.
- Longitud: **400-600 palabras**.

### Artefacto B — Deck para Diego (CEO) en 5 láminas

Vas a tener una reunión de **20 minutos** con Diego. Crea el deck en **Markdown** (no diseño, solo contenido y estructura). 5 láminas, ni una más.

Lámina 1: **Título + un dato impactante**
Lámina 2: **Problema** (en lenguaje de negocio, no de stack)
Lámina 3: **Costo de no actuar** (proyección a 6 meses)
Lámina 4: **Propuesta** (opciones, no "el refactor")
Lámina 5: **Lo que necesito de ti**

Reglas no negociables:
- **La palabra "refactor" no puede aparecer ni una sola vez**. Si la necesitas, busca otra forma de decirlo.
- **Cada lámina debe tener un número o dato** específico, no solo prosa.
- **El deck debe poder leerse en 90 segundos** si Diego solo mira las láminas y no escucha tu explicación.
- **Lámina 4** debe ofrecer **al menos 2 caminos**, no uno solo (Diego rechaza opciones únicas).
- **Lámina 5** debe pedir algo **concreto y pequeño** primero, no la aprobación de las 8 semanas.

Formato:

```markdown
## Lámina 1 — [título]
- Bullet 1
- Bullet 2
- (notas para mi narración: ...)

## Lámina 2 — [título]
...
```

### Artefacto C — FAQ anticipando objeciones

Página de FAQ que **tú** vas a tener a mano (no la entregas) para anticipar objeciones de Carla y Diego.

Estructura: 8-10 objeciones reales + tu respuesta de ≤ 80 palabras cada una.

Ejemplos de objeciones que esperaríamos ver cubiertas (puedes añadir o cambiar):

1. *"¿Por qué no se hizo bien desde el principio?"* (de Diego)
2. *"Y si dejamos solo el cliente que renegocia, ¿no podemos contener la situación?"* (de Carla)
3. *"¿Por qué 8 semanas y no 3 con tu mejor gente?"* (de Diego)
4. *"Tu equipo entrega lento. ¿Cómo me garantizas que no será 16 semanas?"* (de Diego)
5. *"¿Y las 3 features que prometimos a los clientes? ¿Las cancelo yo?"* (de Carla)
6. *"¿No podemos comprar una herramienta que resuelva esto?"* (de Diego)
7. *"¿Por qué no contratamos a alguien junior solo para esto?"* (de Diego)
8. *"Si tan grave es, ¿por qué no me lo dijiste hace 3 meses?"* (de Diego)

Reglas:
- Cada respuesta debe **reconocer la legitimidad** de la objeción antes de responder.
- Nada de jerga técnica.
- Si una respuesta te obliga a decir *"no, eso no se puede"*, dilo claro y ofrece una alternativa.

---

## ⚠️ Anti-patrones que estaremos buscando

- Email a Carla que la pone a la defensiva en el asunto o primera línea.
- Deck que es una **lista de issues técnicos** disfrazada (*"locks de tabla", "queries N+1"*).
- Deck que pide aprobación de las 8 semanas en la primera lámina.
- FAQ que descarta objeciones como ingenuas o las responde con condescendencia.
- Cualquier mención a "deuda técnica" sin traducir.
- Cualquier mención a "refactor" en el deck.
- Plantear el problema como ingeniería vs. negocio, en lugar de ambos vs. el problema.
- Esconder el costo (lo que el negocio pierde si te dan las 8 semanas).
- Pintar a Carla o a Diego como obstáculos en el documento que vas a compartir con ellos.

---

## 🟢 Señales de un buen entregable

- El email a Carla podría **llegar reenviado a Diego sin cambios** y no causarte problemas.
- El deck es algo que Diego, **mirando solo las láminas, podría aprobar o seguir conversando** con base concreta.
- Los datos de la lámina 1-3 vienen de fuentes ya disponibles (tickets de soporte, MRR, NPS) — no inventas métricas.
- La lámina 4 ofrece un camino que **Diego podría elegir aunque no apruebe el plan completo** (ej. piloto, fase 1, sprint de spike).
- La lámina 5 pide algo **realizable en una semana**, no la decisión grande.
- En el FAQ, hay **al menos una objeción donde tu respuesta es "tienes razón en X, y por eso propongo Y"** — no defiendes todo.
- En el FAQ, la pregunta 8 (*"¿por qué no me lo dijiste hace 3 meses?"*) tiene una respuesta honesta que no echa culpa al pasado.
- En conjunto, transmite que **el equipo de ingeniería es socio del negocio**, no su contraparte.
