# Vivero — MVP Overview 🌱

> Documento de visión y decisiones surgido de una sesión de _grilling_ de diseño.
> **No** es el estándar de documentación del proyecto (eso será `CONTEXT.md`, `docs/adr/`, etc.).
> Esto es la **foto congelada** de cómo llegamos al diseño del MVP: el problema, las decisiones y —sobre todo— las alternativas que descartamos y por qué.

---

## 1. El problema (causa raíz)

El objetivo personal es **ponerse las pilas en redes** (Twitter, LinkedIn, blog). Un intento previo con archivos planos (Obsidian) + skills de IA fracasó.

Diagnóstico tras descartar los síntomas superficiales:

- **El cuello de botella NO es crear.** Generar el núcleo de una idea no cuesta (se hace incluso por voz).
- **El cuello de botella es la última milla:** los borradores se quedaban en "draft" y de ahí **no pasaban nunca**.
- **Problema secundario — diógenes digital:** la inspiración (algo que se vio/leyó/escuchó) se dispersa porque no hay un sitio cómodo donde tirarla en caliente.
- **Falta un sistema que acompañe el flujo completo**, del impulso a la publicación, y que confronte cuando algo se atasca.

> **Conclusión de diseño:** el esfuerzo NO va en "mejor captura + mejor IA para escribir". Va en **cerrar la última milla** (que las cosas salgan) y en **centralizar la inspiración**.

---

## 2. La palanca (función de forzado)

> _"Palanca"_ es nuestro término de conversación para la **función de forzado**: el mecanismo que convierte la fecha autoimpuesta en algo con consecuencia. No pretende llegar a la UI ni al código.

Lo que mató el flujo anterior fue la **falta de presión**: un draft es una promesa blanda a uno mismo, sin deadline ni consecuencia, así que nunca se cumple.

Pero los **slots fijos** (push: "publicas martes y jueves sí o sí") se **descartaron** → generan presión innecesaria cuando no hay ideas suficientes.

**Modelo elegido: pull con palanca mínima.**

- **Pull:** uno decide, idea por idea, cuándo quiere publicar (fecha objetivo por Publicación). Es "una promesa a mí mismo".
- **La palanca:** cuando pasa la fecha sin publicar, la Publicación entra en estado **`vencida`** (rojo) que **no se autolimpia**. Fuerza una decisión explícita: publicar / reprogramar / matar. El coste de tener que *decidir matarla* duele más que dejarla pudrirse en silencio.
- **Las Ideas nunca vencen** — acumular ideas es sano, no es un compromiso.

**Refuerzo positivo (gamificación):** se reconoció que una palanca puramente negativa no basta. El refuerzo positivo que aguanta es **ver crecer el propio "cuerpo de obra"** (identidad: "soy alguien que publica"), NO rachas ni puntos (peligrosos para un perfil que abandona). **Aplazado fuera del MVP**, anotado como _hint_ de futuro.

---

## 3. Modelo de dominio (lenguaje ubicuo)

Lenguaje ubicuo en **español**; identificadores de código en **inglés** (mantener un glosario 1:1, p. ej. `vencida → overdue` siempre).

Son **tres entidades conectadas**, NO un único ítem que cambia de columna estilo Kanban (eso se descartó explícitamente).

### Inspiración (`Inspiration`)
Material **externo** que se guarda (vídeo, post de RRSS, artículo, podcast…). Vive por sí sola; su valor real es **alimentar Ideas**.

- `id`
- `title` — etiqueta corta legible
- `url?` — opcional (casi siempre habrá, pero se permite sin enlace, p. ej. algo que te contaron)
- `status` — `no visto | visto` (cubre el caso "guardar para ver/leer luego")
- `notes` — texto corto (por qué se guardó, qué sugirió)
- `createdAt`
- Relación **M:N** con Idea.

> Frontera del lenguaje: una reflexión propia o algo que pasó en el trabajo **no** es Inspiración → es directamente el núcleo de una **Idea**. Inspiración = material externo.

### Idea (`Idea`)
El **núcleo conceptual** — "lo que quiero decir". **Sin fecha**, es el aparcamiento sano donde germinan los brotes. Se escribe a mano.

- `id`
- `title`
- `file` — puntero al markdown con el núcleo
- `createdAt`
- Relación **M:N** con Inspiración (a nivel de tronco, no de pieza).
- Relación **1:N** con Publicación.

### Publicación (`Publication`)
Unidad de contenido concreta **para un canal**, derivada de una Idea. **Siempre tiene fecha** (= el compromiso).

- `id`
- `title` — **opcional al crear** (la IA puede generarlo en el _oneshot_)
- `ideaId` — **obligatorio** (toda Publicación cuelga de una Idea)
- `formatId` — FK al Formato
- `status` — `borrador | listo para publicar | publicado`
- `targetDate` — **obligatoria** (el compromiso)
- `publishedAt?` — se rellena al marcar publicado
- `file` — puntero al markdown del contenido (lo edita la IA, lo remata el usuario)
- `referencedPublicationId?` — solo si el formato es _complementario_ (a qué Publicación anuncia)
- `createdAt`
- **`overdue` NO es un campo:** se calcula al vuelo = `status ∈ {borrador, listo} AND targetDate < hoy AND no publicado`.

### Formato (`Format`)
Preset que transforma una Idea en una Publicación. **Tabla en BBDD** (opción elegida sobre "descubrir desde la carpeta de skills", por dar una referencia estándar y estable que sobrevive a cambios de _approach_).

- `id`
- `name` — p. ej. "Twitter — teaser"
- `channel` — LinkedIn / Twitter / blog
- `type` — `autónomo | complementario`
- `skillSlug` — puntero al **skill** del proyecto que genera ese formato

> El **prompt NO vive en BBDD**: vive en un **skill** del proyecto (versionable en git, iterable por la propia IA, portable a otros agentes). El `skillSlug` es solo el puntero de "cómo se genera".

### Modelo de tronco-y-ramas (clave)
Una Idea es un **tronco** del que cuelgan varias Publicaciones (1:N), cada una con su canal, su fecha y su ciclo propio. Justificación: una misma idea encaja con distintos targets (algo breve → Twitter, mejor redactado → LinkedIn, extenso → blog), y además unas piezas **anuncian** a otras (el post de LinkedIn que anuncia el artículo del blog; el teaser de Twitter que referencia el post de LinkedIn).

- La relación **Publicación → Publicación** ("esta anuncia a aquella") existe **en datos** (para que la IA tenga contexto al generar la complementaria) pero **NO necesita UI rica** (basta el título). Se descartó modelar enlaces dirigidos tipados con reglas de orden.

### Formatos iniciales

| Formato | Canal | Tipo |
|---|---|---|
| Artículo de blog | blog | autónomo |
| LinkedIn — post principal | LinkedIn | autónomo |
| Twitter — hilo | Twitter | autónomo |
| Twitter — tweet individual | Twitter | autónomo |
| LinkedIn — anuncio de artículo | LinkedIn | complementario |
| Twitter — teaser | Twitter | complementario |

> "Formato" ≠ "canal" (Twitter solo ya tiene 3 formatos). Pero **no** es una matriz `canal × formato`: es una **lista plana de presets**. Los _complementarios_ son justo los que requieren `referencedPublicationId`.

---

## 4. Ciclo de vida y reglas

- **Idea = aparcamiento sin fecha.** Pozo libre, sin presión, sin palanca. (Sin estado `archivada` en el MVP; si hace falta, migración futura.)
- **Publicación = siempre con fecha.** No existe la "Publicación sin fecha". Consecuencia clave: **no hay zona de muerte de drafts** — o es una Idea (no pretende ser compromiso) o es una Publicación con fecha (compromiso real).
- Estados de Publicación: `borrador → listo para publicar → publicado`.
  - `listo para publicar` se añadió pensando en una futura feature de programación de posts.
  - `publicado` es **marcado manual** en el MVP (no hay API de publicación todavía).
- `vencida` (derivado) muerde también a `listo para publicar` (estaba listo y aun así no salió → el caso más sangrante).

---

## 5. Flujo de generación con IA

- **Generar es opcional y bajo demanda**, pero el **camino feliz** (casi siempre) es generar un _oneshot_ con IA al crear.
- **Un solo gesto** crea + genera: desde una Idea → "nueva Publicación" → eliges **formato + fecha objetivo** (+ pieza referenciada si es complementario, + **instrucción puntual opcional**) → un _oneshot_ genera **título y contenido** → nace en `borrador`.
  - Mala UX a evitar: obligar a "crear" (con título obligatorio) y luego "generar" en dos pasos. El título lo puede poner la IA.
  - Lo único obligatorio para crear el compromiso: `formatId + targetDate` (+ `referencedPublicationId` si complementario).
- **Escape hatch:** si no hay _usage_ de Claude o se quiere escribir algo a mano de principio a fin, se rellena el archivo manualmente. La IA no es obligatoria.
- **Refinar:** "generar con IA" es una acción **repetible** sobre una Publicación existente (regenerar con otra instrucción, o refinar lo ya escrito).
- **Instrucción puntual** ("este tweet va de esta parte de la idea"): **transaccional**, viaja con el _job_ headless, **no se persiste** en BBDD.
- **Asíncrono:** mientras Claude Code trabaja, la Publicación lleva un **flag transitorio `generando`** (no es un estado del ciclo). Si **falla**, vuelve a `borrador` con su archivo como estuviera y se puede reintentar. Nunca se pierde el compromiso por un fallo de IA.

---

## 6. Arquitectura

- **App web local, single-user, sin auth.** (Si algún día la usan otros, se reverá entonces; no se diseña para ello ahora.)
  - _Por qué web local y no archivos planos (la alternativa descartada):_ (a) los archivos planos (Obsidian) **ya se abandonaron** una vez → evidencia fuerte de que ese entorno no engancha para este uso; (b) los dos mecanismos clave —el `vencida` en rojo y el futuro "cuerpo de obra"— son **visuales**, y en markdown plano no se _sienten_; una UI sí los hace sentir.
- **Servidor Bun desde el día 1.** Decisión clave: construir como un servidor HTTP de Bun normal (no un montaje solo-Vite-en-dev) para que empaquetar al final sea indoloro.
- **Persistencia híbrida:**
  - **SQLite** = el cerebro de gestión: entidades, relaciones, estados, fechas, la palanca.
  - **Archivos markdown** = el cuerpo del contenido. **Idea** y **Publicación** son _file-backed_ (puntero por entidad). **Inspiración** vive solo en SQLite.
  - Ventaja: el archivo es el formato universal → desacopla el contenido del agente (hoy Claude Code, mañana otro).
- **IA:** la app **shellea Claude Code en modo headless** (`claude -p` / Agent SDK) ejecutando el **skill del formato**, pasándole las referencias (idea, pieza referenciada si aplica, instrucción puntual). El agente **edita el archivo in situ**; la app lo relee. (Se descartó la API directa de Anthropic para aprovechar la suscripción de Claude Code; se descartó el copy-paste manual por fricción.)
- **Cáscara de arranque (se construye AL FINAL, es una capa externa pura):** `bun build --compile` → binario único + **LaunchAgent (launchd)** en background + abre el navegador. (Opción "app de escritorio Tauri" aplazada como _upgrade_ futuro.)

> **Decisión técnica pendiente (próxima sesión):** el **stack del frontend** sobre el servidor Bun (framework de UI) y el **ORM/driver de SQLite** quedaron deliberadamente sin decidir. Encajan mejor en una sesión de prototipado/planificación técnica, que es donde estas decisiones se toman mejor viéndolas. Lo único cerrado a nivel técnico es lo necesario (servidor Bun, SQLite + archivos, IA por Claude Code headless, cáscara al final).

---

## 7. Vistas del MVP

- **Lista por urgencia** (hogar): Publicaciones ordenadas, **vencidas arriba en rojo**, sin poder ocultarse scrolleando. (El **calendario** se aplazó a futuro; se prefirió empezar con lista.)
- **Bandeja de Ideas** (aparcamiento sin fecha) — separada.
- **Bandeja de Inspiración** — separada (primero "no visto", luego "visto").
- **Detalle de Idea** — núcleo + sus Publicaciones (vista por familia/tronco) + Inspiraciones enlazadas.
- **Detalle de Publicación** — contenido + estado + formato/canal + fecha + referencias.

> La mayor parte del detalle de UI/UX se decide en **sesiones de prototipado posteriores** (es donde se toman mejor estas decisiones, viéndolas).

---

## 8. Fuera del MVP (anotado para después)

- **Captura desde móvil** (la inspiración nace en el móvil; opción favorita: buzón tipo **bot de Telegram** que la app ingiere). Reconocido como importante, aplazado.
- **Vista calendario**.
- **Gamificación / "cuerpo de obra"** (refuerzo positivo por identidad) + **métrica de cumplimiento**.
- **Auto-publicación vía APIs** y **programación de posts** (para eso se dejó el estado `listo para publicar`).
- **App de escritorio (Tauri)**.
- **`type` de Inspiración** (inferible de la URL al mostrar, no se guarda).
- **Estado `archivada` en Idea** (migración futura si el pozo se vuelve cajón desastre).
- **Gestión del "cajón desastre" de Inspiración** (qué pasa con las vistas hace tiempo que no fueron a ninguna Idea: ocultar / dejar muertas / borrar) → decisión de UI para prototipado.
- **Relaciones Publicación↔Publicación tipadas/visualizadas en UI** (en datos sí, como puntero para contexto de IA).
- La mayor parte de la **UI fina**.

---

## 9. El nombre 🌱

**Vivero**: el lugar donde los brotes germinan y se cuidan **antes de trasplantarlos al mundo** — justo lo que la app hace con las **Ideas** antes de que salgan como **Publicaciones**. Humilde, fácil de decir, suena internacional, y la metáfora se sostiene sola.
