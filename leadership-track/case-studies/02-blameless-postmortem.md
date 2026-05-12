# Case Study 02 — Postmortem Blameless

> **Lo que evaluamos**: cultura blameless, comunicación a stakeholders, cómo conduces la conversación 1:1 con el responsable directo.
> **Lo que NO evaluamos**: análisis técnico profundo del incidente. La causa ya te la damos; no inventes "root causes" técnicas.

---

## 🎬 Contexto

Eres el líder de un equipo de 5 personas en una empresa de e-learning. Tu plugin sirve recursos educativos a ~40.000 usuarios diarios. Hace **3 días** hubo un outage de **47 minutos** en horario pico.

### Lo que pasó (los hechos)

- **Sábado 11:18** — **Marcos** (senior con 3 años en el equipo) mergeó un PR de migración de tabla. El PR había estado abierto 4 días esperando review. Marcos lo aprobó **a sí mismo** y mergeó porque "ya nadie estaba conectado un sábado y la migración era urgente para la demo del lunes".

- **11:22** — Marcos ejecutó la migración en producción. Lock de tabla más largo de lo esperado por el volumen de datos.

- **11:26** — Empezaron los 5xx. Los healthchecks fallaron. PagerDuty alertó a Marcos.

- **11:33** — Marcos detectó el problema, intentó rollback. La caché que había confiado en recuperarse no lo hizo (cache stampede al volver: miles de requests pegando a la BD a la vez).

- **12:05** — Resuelto, después de subir más replicas y forzar warmup del cache.

- **Duración total**: 47 min con error rate > 50%.

- **Impacto**: ~2.300 usuarios afectados en el rango. Una empresa cliente nos escribió quejándose. Soporte recibió 18 tickets. Twitter mencionó la caída 4 veces.

### El estado actual del equipo (3 días después)

- **Está dividido**:
  - 2 personas (incluido un senior) opinan que *"Marcos fue irresponsable, mergeó sin review, esto no se hace, hay que sancionar"*.
  - 2 personas opinan que *"el proceso es lo que está mal: si no hay nadie disponible un sábado y la pieza se necesita, alguien iba a tener que cortar esquinas. Marcos hizo lo que cualquier de nosotros".*
  - 1 persona no se ha posicionado.

- **Marcos** está mal. Ha estado callado en standup, escribió un mensaje en privado a tu jefe asumiendo "toda la responsabilidad". Te dijo en pasillo *"si quieres puedo coger un día de vacaciones, no me siento bien aquí ahora mismo".*

- **Tu jefe** te pregunta *"¿qué medida vamos a tomar?"*

- **El cliente que se quejó** quiere una explicación escrita en 72h.

- **El equipo** está esperando ver qué hacés. Mañana es lunes. Tienes que actuar.

---

## 📝 Tu entregable

Produce **tres artefactos**, en este orden.

### Artefacto A — Postmortem público interno

Documento en Markdown que vas a publicar en el wiki/Notion/Confluence del equipo, accesible para toda la ingeniería de la empresa.

Estructura sugerida (puedes adaptarla pero cubre todo):

```markdown
# Postmortem: Outage del sábado [fecha]

## Resumen ejecutivo
[2-3 líneas]

## Timeline
[Con timestamps]

## Impacto
[Usuarios, clientes, soporte, comms externas]

## Qué falló
[Factores contribuyentes — NO "root cause"]

## Qué funcionó bien
[Sí, esto es obligatorio]

## Cómo se detectó
## Cómo se mitigó
## Cómo se resolvió

## Action items
[Tabla con: acción, dueño, fecha objetivo, tipo (prevención/detección/mitigación)]

## Lecciones aprendidas
## Apéndice: glosario / enlaces
```

**Reglas no negociables**:
- **Sin nombres en culpa**. Sí en acciones positivas (quien detectó, quien mitigó).
- **"Factores contribuyentes" en plural**, no causa raíz singular.
- Acción de proceso debe haber. *"Capacitar mejor a Marcos"* no es una acción válida.
- Cero pasivos disfrazados (*"se decidió hacer..."*) — di qué pasó, con qué información.
- La sección "qué funcionó bien" no puede estar vacía ni ser auto-laudatoria.

### Artefacto B — Comunicación al cliente externo

Email (o post de status page) que vas a enviar al cliente afectado. Reglas:

- Honesto. No minimices.
- Específico. Dile qué pasó en términos que él pueda entender, no "tuvimos un problemita técnico".
- Reparativo. ¿Qué compensación o garantía ofreces? (no necesariamente dinero — puede ser proceso, transparencia, acceso a status page).
- Forward-looking. Qué van a cambiar para que no se repita.
- **No menciones nombres**.

### Artefacto C — Plan del 1:1 con Marcos

Lunes por la mañana vas a tener un 1:1 con Marcos (30 min, agendado por ti, no por él).

Entrega:

- **Cómo preparas el 1:1**: qué te dices a ti mismo antes de entrar. Qué hipótesis tienes sobre cómo Marcos está procesando esto.

- **Cómo abres**: frase inicial concreta. (Pista: no es *"vamos a hablar del incidente"*.)

- **3 preguntas que harás** en la conversación. Que sean reales, no retóricas.

- **Cómo manejas el momento si Marcos**:
  - Se hunde más y se autoflagela.
  - Se defiende agresivamente echando culpa al proceso.
  - Te pide formalmente "una sanción" para sentirse "absuelto".

- **Cómo cierras**: con qué se va Marcos del 1:1.

- **Una decisión** que **NO** discutes con él en ese 1:1, pero que tienes que tomar tú esta semana (¿qué cambia operativamente — quién mergea sin review nunca más, política de fin de semana, etc.?).

---

## ⚠️ Anti-patrones que estaremos buscando

- Postmortem que parece blameless pero todos los action items son "Marcos hará X".
- Email al cliente que minimiza el impacto o usa lenguaje corporativo vacío.
- 1:1 con Marcos donde le "perdonas" la falla — eso no es blameless, es paternalismo.
- 1:1 donde le pides que "haga un análisis profundo de lo que pasó" — eso es delegarle el trabajo emocional del líder.
- Aceptar la oferta de Marcos de coger días sin abordarlo emocionalmente primero.
- Sancionar a Marcos como gesto político para calmar al subgrupo que pide cabeza.
- Ignorar a la otra mitad del equipo que opina que el proceso es el problema.

---

## 🟢 Señales de un buen entregable

- El postmortem se podría compartir con **toda la empresa, Marcos incluido**, y nadie sentiría que está siendo lapidado ni que se está encubriendo.
- Los action items son cambios de **sistema** (proceso, herramienta, regla), no de **persona** ("Marcos será más cuidadoso").
- El email al cliente es algo que **tú firmarías con tu nombre** y al cliente le **subiría** la confianza, no se la bajaría.
- El 1:1 con Marcos termina con él sintiendo:
  - Que el incidente fue serio (no minimización).
  - Que no es el monstruo de la historia.
  - Que hay un cambio concreto en el sistema que él va a ver implementarse.
  - Que su lugar en el equipo no está en cuestión.
- Hay una **decisión operativa** que tomas tú esta semana **sin pedirle a Marcos su opinión**, porque es tu trabajo y no quieres descargar la responsabilidad en él.
