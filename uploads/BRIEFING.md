# Briefing — Deck Workshop Claude + Obsidian

> Documento de contexto del estado actual del deck para iterar sobre él en una sesión de Claude. Pegá este archivo entero al inicio de la conversación nueva.

---

## 1. Contexto del evento

- **Nombre**: Workshop · Claude + Obsidian — Construí tu second brain con IA
- **Edición**: 01
- **Fecha**: 14 de mayo de 2026, 17:00 – 19:00
- **Sede**: UCEMA · Av. Córdoba 374, Buenos Aires (CP 1054), Aula Magna
- **Anfitriones**: The AI Collective BA · Xplora UCEMA
- **Aliados**: Anthropic (charla introductoria a Claude) · Galo (challenge final)

### Speakers e invitados

| Bloque | Speaker | Rol | Empresa |
|---|---|---|---|
| Charla introductoria a Claude | Cristian Cabrera | Founder | We Make It Lab (partner Anthropic) |
| Workshop principal | Jimmy Adaro | Co-founder, Tech & Ops | Lideki · Founder Sin Jr No Hay Sr (+165K) |
| Caso práctico | Luciano Esteban | Arquitecto de Ciberseguridad | Personal |
| Challenge final | Franco Pesce | CTO | Galo |

### Agenda (cargada en slide 04)

| Hora | Bloque | Quién |
|---|---|---|
| 17:10 | Apertura | The AI Collective BA + Xplora UCEMA |
| 17:20 | Visión de la comunidad | Introducción al evento + contexto |
| 17:30 | Charla introductoria de Claude | Anthropic · Cristian Cabrera |
| 17:40 | Workshop principal | Jimmy Adaro |
| 18:15 | Caso práctico: Claude + Obsidian | Personal · Luciano Esteban |
| 18:30 | Presentación del challenge | Galo · Franco Pesce |
| 18:40 | Actividad práctica, mentoreo y cierre | Trabajo en vivo + Q&A |

---

## 2. Stack técnico

- **Tipo**: deck HTML estático, sin build, sin framework.
- **Archivo principal**: `index.html` (1692 líneas, ~56 KB).
- **Componente custom**: `deck-stage.js` (web component reutilizable, ~72 KB).
  - Auto-escalado: canvas fijo 1920×1080, escala con `transform: scale()` y letterbox.
  - Navegación: ← →, PgUp/PgDn, Espacio, Home/End, números, R para reset.
  - Speaker notes vía `<script type="application/json" id="speaker-notes">`.
  - Thumbnail rail con drag-to-reorder, skip, delete (persiste ancho en localStorage).
  - Print: `@media print` produce 1 página por slide → PDF limpio con Cmd/Ctrl+P.
  - Las slides se ocultan, no se desmontan (preserva estado de videos/iframes/inputs).
  - Dispara `slidechange` CustomEvent con `{index, previousIndex, total, slide, reason}`.
- **Assets**:
  - `assets/aicollective-logo.png` (108 KB) — único logo presente.
  - `assets/workshop-poster.jpeg` (164 KB) — afiche original que inspiró la paleta.

---

## 3. Sistema de diseño

### Paleta (extraída del afiche del workshop)

```css
--navy:        #2b3a4a   /* dark navy texturizado, primary dark */
--navy-deep:   #1d2935
--navy-soft:   #3c4d5f
--orange:      #f97316   /* acento principal */
--orange-soft: #fb923c
--cream:       #F4F3EE   /* fondo claro principal */
--cream-deep:  #ebe8df
--terra:       #C15F3C   /* terracota para blobs/rings */
--ink:         #1a232e   /* texto sobre cream */
--mute-on-dark:   #a4b2c0
--mute-on-cream:  #6d5d52
--rule-on-dark:   rgba(255,255,255,0.14)
--rule-on-cream:  #d8d2c4
```

### Tipografías (Google Fonts)

```css
--display: "DM Serif Display", "Playfair Display", serif;   /* titulares grandes */
--serif:   "Playfair Display", "Times New Roman", serif;    /* itálicas, énfasis */
--sans:    "Manrope", "Helvetica Neue", Arial, sans-serif;  /* body */
--mono:    "JetBrains Mono", ui-monospace, monospace;       /* eyebrows, mono */
```

### Escala tipográfica

```css
--type-display:  200px   /* hero portada */
--type-title:   112px   /* H1 de slides editoriales */
--type-h2:       80px   /* H2 de slides de contenido */
--type-subtitle: 44px
--type-body:     28px
--type-small:    24px
--type-eyebrow:  24px   /* mono, uppercase, letter-spacing 0.18em */
```

### Spacing y padding

```css
--pad-x: 120px;       /* padding horizontal del frame */
--pad-top: 90px;
--pad-bottom: 90px;
```

### Componentes CSS reutilizables

- **`.frame`** — contenedor interno con `padding: var(--pad-top) var(--pad-x) var(--pad-bottom)`, flex column.
- **`.topbar`** — mono, uppercase, letter-spacing 0.18em. Lleva `.mark` con dot naranja a la izquierda y meta de slide a la derecha.
- **`.pill`** — píldora redonda con `--mono`, uppercase. Variante `.pill.solid` con fondo naranja.
- **`.bg-navy`** — fondo navy texturizado con dos repeating-linear-gradients (115° y 25°) en `mix-blend-mode: overlay`, más radial gradients para luz.
- **`.bg-cream`** — fondo cream con stripe vertical izquierdo (16px) en gradient navy→orange y un repeating-linear-gradient terracota muy sutil (4% opacidad).
- **`.bg-cream-blue`** — variante con stripe lateral navy/orange.
- **`.beam`** — haz de luz diagonal (rotate -22deg, blur 28px) que emula los rayos del afiche.
- **`.ribbon`** — cinta iridiscente SVG (gradient naranja→rosa→azul) top-left, blur 2px.
- **`.blob`** — círculo terracota con opacity 0.08, decorativo.
- **`.ring`** — círculo borde terracota 1.5px, opacity 0.35, decorativo.
- **`.hero-1`** — fondo radial dual (azul + naranja) sobre navy para portada.
- **`.session-divider`** — split izquierda (numeración grande 01/02/03 + título) / derecha (`.speaker-card` con role, name en serif italic, bio).
- **`.content-placeholder`** + **`.slot`** — patrón estándar de slide vacía con eyebrow, H2, y caja punteada con "[ Contenido por desarrollar ]".
- **`.agenda-table`** + **`.agenda-row`** — tabla de itinerario (hora, qué, quién, lugar).
- **`.sponsors-tier`** + **`.row`** + **`.logo-slot-cream`** — grid de logos por tier.
- **`.bases-grid`** + **`.bases-list`** — grid 2-col para slide 18 (intro + lista numerada 01-05).

### Estilo visual general

- Editorial / revista (DM Serif Display + Manrope).
- Itálicas en serif para palabras clave dentro de titulares (ej. *"Construi tu **second brain** con IA"*).
- Acentos naranja sobre navy, terracota como decorativo sutil sobre cream.
- Slides cream con stripe lateral vertical 14-16px en gradient navy→orange = sello visual recurrente.
- Texturas sutiles (líneas diagonales, radial gradients) en lugar de fondos planos.

---

## 4. Inventario de slides (18 total)

Leyenda: ✅ terminada · 🟡 estructura ok, contenido placeholder · 🔴 contenido por desarrollar.

### Slide 01 — Bienvenida ✅ `class="hero-1"`
Hero a pantalla completa, fondo navy con gradients.
- Branding: logo AI Collective + "×" + "Xplora · UCEMA" como co-host.
- Display: "Workshop · Edición 01 · 14.05.2026" / **"Construi tu *second brain* con IA."**
- Meta row: Fecha 14.05 · Horario 17-19h · Sede UCEMA · Stack Claude + Obsidian.
- Aliados: caja con "A\ ANTHROPIC" y "GALO" (texto plano, sin logos reales).

### Slide 02 — AI Collective: El poder de la comunidad 🔴 `class="bg-navy"`
Fondo navy con ribbon iridiscente top-left + beam.
- Topbar: "Workshop · Claude + Obsidian" | "02 / 18 · Comunidad"
- H1: **"AI Collective: el *poder* de la comunidad."**
- Lede: "Una red de practicantes que aprende, comparte y construye con **IA** en Buenos Aires."
- Slot grande: `[ Contenido por desarrollar ]` — "Manifiesto, métricas y propósito de la comunidad."

### Slide 03 — Anfitriones y Aliados 🟡 `class="bg-cream"`
- Topbar: "03 / 18 · Aliados"
- H2: **"Red de *innovación*: anfitriones y aliados."**
- Kicker: "Dos anfitriones, tres aliados y una comunidad que aporta **sede, herramientas y mentoreo**."
- Tier "Anfitriones" (02 marcas): THE AI COLLECTIVE BA · XPLORA · UCEMA (logo slots con texto, sin imágenes).
- Tier "Aliados" (02 marcas): A\ · ANTHROPIC · GALO (logo slots con texto, sin imágenes).

### Slide 04 — Itinerario ✅ `class="bg-cream"`
- Topbar: "04 / 18 · Itinerario"
- H2: **"Itinerario: agenda del *encuentro*."**
- Kicker: "Siete bloques cortos: charla, workshop, caso práctico y un **challenge final** con mentoreo."
- `.agenda-table` con las 7 filas detalladas arriba en sección 1.

### Slide 05 — Sesión Anthropic · divider ✅ `class="bg-cream"`
- Topbar: "05 / 18 · Sesión 01"
- `.session-divider`: num "01" + "Sesión Anthropic" + H1 placeholder *"Título del tema."*
- Speaker card: Cristian Cabrera. Role: "Charla introductoria · Anthropic". Bio: *"Founder de We Make It Lab y partner oficial de Anthropic. Abre el evento con una mirada introductoria a Claude y al stack de la compañía."*
- ⚠️ Falta título real de la charla.

### Slide 06 — Anthropic 02 🔴 `class="bg-cream"`
- Topbar: "06 / 18 · Sesión Anthropic"
- Eyebrow: "Sesión 01 · Anthropic — Cristian Cabrera"
- H2 placeholder: "Título del *tema* 02 de 03."
- Slot vacío.

### Slide 07 — Anthropic 03 🔴 `class="bg-cream"`
Idéntica estructura a la 06. "Título del tema 03 de 03." Slot vacío.

### Slide 08 — Sesión Jimmy · divider ✅ `class="bg-cream"`
- Topbar: "08 / 18 · Sesión 02"
- `.session-divider`: num "02" + "Workshop principal" + H1 placeholder.
- Speaker card: Jimmy Adaro. Role: "Workshop principal · Lideki". Bio: *"Co-founder, Tech & Ops en Lideki. Founder de Sin Jr No Hay Sr (+165K). Lleva el corazón del workshop con demo en vivo de Claude + Obsidian."*
- ⚠️ Falta título real del workshop.

### Slides 09–13 — Jimmy 02 a 06 🔴 `class="bg-cream"`
Cinco slides idénticas en estructura: eyebrow "Sesión 02 · Workshop principal — Jimmy Adaro" + H2 "Título del tema X de 06" + slot vacío.

### Slide 14 — Sesión Luciano · divider ✅ `class="bg-cream"`
- Topbar: "14 / 18 · Sesión 03"
- `.session-divider`: num "03" + "Caso práctico" + H1 placeholder.
- Speaker card: Luciano Esteban. Role: "Caso práctico · Personal". Bio: *"Arquitecto de Ciberseguridad en Personal. Comparte cómo aplica Claude + Obsidian en su flujo diario de trabajo y aprendizaje."*
- ⚠️ Falta título real del caso.

### Slides 15–16 — Luciano 02 y 03 🔴 `class="bg-cream"`
Dos slides idénticas: eyebrow "Sesión 03 · Caso práctico — Luciano Esteban" + H2 "Título del tema X de 03" + slot vacío.

### Slide 17 — Desafío Galo · divider ✅ `class="bg-cream"`
- Topbar: "17 / 18 · Desafío"
- `.session-divider`: num "04" + "Presentación del desafío" + H1 **"El *desafío* Galo."**
- Speaker card: Franco Pesce. Role: "Challenge final · Galo". Bio: *"CTO de Galo. Lidera el diseño del desafío y el mentoreo en vivo. Cuenta el contexto del problema y las reglas de juego antes del trabajo en sala."*

### Slide 18 — Bases y Objetivos 🟡 `class="bg-cream"` (cierre, `data-deck-last-visible`)
- Topbar: "18 / 18 · Bases del desafío"
- Pill terracota: "Challenge · Bases"
- H2: **"Bases y *objetivos*."**
- Lede: "Reglas, criterios y entrega del challenge final junto a **Galo**. Aplicá lo trabajado durante el workshop a un caso real, en formato individual o por equipo."
- Lista numerada 01-05 (todas con `[ Por desarrollar ]`):
  1. **Objetivo del desafío** — descripción del problema y resultado esperado.
  2. **Modalidad de entrega** — formato, plataforma, horario límite.
  3. **Criterios de evaluación** — claridad, uso de Claude + Obsidian, originalidad.
  4. **Premios y reconocimientos** — detalle de premios y **sorteo de cuentas de Claude**.
  5. **Mentoreo y consultas** — cómo y cuándo consultar a mentores.

---

## 5. Estado consolidado

### ✅ Terminadas (5)
01 Portada · 04 Itinerario · 08 Divider Jimmy · 14 Divider Luciano · 17 Divider Galo

### 🟡 Estructura completa, falta detalle (4)
- 03 Anfitriones: faltan los logos reales (Xplora UCEMA, Anthropic, Galo). Solo está el PNG de AI Collective en `/assets`.
- 05 Divider Anthropic: bio ok, falta título de la charla de Cristian.
- 18 Bases: estructura ok, todos los puntos 01-05 con `[ Por desarrollar ]`.
- Portada (01): el ally box muestra `A\` y `GALO` como texto plano, sin logo.

### 🔴 Placeholders sin contenido (9)
02 AI Collective · 06 Anthropic 02 · 07 Anthropic 03 · 09-13 Jimmy 02-06 · 15 Luciano 02 · 16 Luciano 03

### Otras inconsistencias menores
- README dice que el archivo principal se llama `Cumbre Innovacion 2026.html`, pero en el repo está como `index.html`.
- Algunos `</section>` están pegados al siguiente `<section>` sin newline (líneas 1218, 1556) — funciona pero ensucia el diff.

---

## 6. Estructura de archivos del repo

```
.
├── README.md                        — descripción y modo de uso
├── index.html                       — deck (1692 líneas)
├── deck-stage.js                    — web component (escalado + nav + print)
├── assets/
│   ├── aicollective-logo.png        — único logo presente
│   └── workshop-poster.jpeg         — afiche original
└── BRIEFING.md                      — este documento
```

Repo: <https://github.com/franjjm/Presentacion_AICollective>

---

## 7. Para la próxima sesión

Cuando le pases esto a Claude, dale también:
- `index.html` (para editar directo el código).
- Idealmente `deck-stage.js` si querés tocar comportamiento (navegación, thumbnails, print).
- Cualquier asset nuevo (logos faltantes, imágenes) que quieras incorporar.

Y arrancá la conversación nueva diciéndole qué bloque querés trabajar primero (ej. *"vamos a llenar las slides 02 y 18 con contenido real"*, o *"quiero rediseñar la portada"*).
