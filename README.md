# ArtVerse — Virtual AI Art Gallery

> An immersive, browser-based virtual art gallery where AI narrates every masterpiece, visitors navigate between curated works, zoom into fine details, and experience ambient gallery audio — all without leaving the browser.

## Live Demo

**[Open ArtVerse Gallery →](https://sneha-chandra.github.io/virtual-art-gallery)**

---

## What Is ArtVerse?

ArtVerse is a **full-stack browser experience** that transforms your screen into a virtual art gallery. Visitors move through 8 curated masterworks displayed in a cinematic exhibition hall. When you stop at any artwork, an **AI curator speaks aloud** — narrating the history, technique, and emotional depth of the piece using the browser's built-in Text-to-Speech engine. The gallery includes image zoom, a guided tour mode, and procedurally generated ambient gallery audio.

---

## System Architecture

```
┌──────────────────────────────────────────────────────────────────────┐
│                        ArtVerse — Browser Runtime                    │
│                                                                      │
│   ┌──────────────────────────────────────────────────────────────┐   │
│   │                     Data Layer                               │   │
│   │                                                              │   │
│   │  ARTWORKS[]  ← 8 curated masterworks                        │   │
│   │  Each entry: title, artist, year, medium, dimensions,       │   │
│   │              location, image URL, description, narration     │   │
│   └──────────────────────────┬───────────────────────────────────┘   │
│                               │                                      │
│   ┌───────────────────────────▼──────────────────────────────────┐   │
│   │              Gallery Navigation Engine                       │   │
│   │                                                              │   │
│   │  currentIndex ← pointer into ARTWORKS[]                     │   │
│   │  navigate(±1) ← updates all 3 slots (prev/current/next)     │   │
│   │  goTo(i)      ← jump to specific artwork                    │   │
│   │  Room dots    ← clickable progress indicators               │   │
│   └───────────────────────────┬──────────────────────────────────┘   │
│                               │                                      │
│          ┌────────────────────┼────────────────────┐                 │
│          ▼                    ▼                    ▼                 │
│   ┌────────────┐   ┌─────────────────┐   ┌──────────────────┐       │
│   │ CSS 3D     │   │  AI Narration   │   │  Ambient Audio   │       │
│   │ Gallery    │   │  Engine (TTS)   │   │  Generator       │       │
│   │            │   │                 │   │                  │       │
│   │ perspective│   │ SpeechSynthesis │   │ Web Audio API    │       │
│   │ rotateY()  │   │ API             │   │ OscillatorNode   │       │
│   │ Ken Burns  │   │ Voice selection │   │ BiquadFilter     │       │
│   │ animation  │   │ Rate/pitch tune │   │ 5-harmonic drone │       │
│   │ Spotlight  │   │ Guided tour     │   │ Fade in/out      │       │
│   │ effect     │   │ auto-advance    │   │ Gallery ambiance │       │
│   └────────────┘   └─────────────────┘   └──────────────────┘       │
│                                                                      │
│   ┌──────────────────────────────────────────────────────────────┐   │
│   │            Canvas Image Detail View                          │   │
│   │                                                              │   │
│   │  HTML5 Canvas 2D ← renders artwork at full resolution       │   │
│   │  Zoom: wheel event → scale transform (0.5× to 8×)          │   │
│   │  Pan:  mousedown + mousemove → translate origin             │   │
│   │  Touch: touchmove → pan on mobile devices                   │   │
│   │  Reset: double-click → zScale=1, panX=0, panY=0            │   │
│   └──────────────────────────────────────────────────────────────┘   │
└──────────────────────────────────────────────────────────────────────┘
```

---

## Project File Structure

```
virtual-art-gallery/
├── index.html          # Complete gallery — HTML, CSS, JavaScript, artwork data
└── README.md
```

> Single-file architecture: the entire gallery — 3D display, AI narration, Canvas zoom, Web Audio — is in one HTML file deployable to any static host.

---

## Features

### 🖼 Immersive 3D Gallery Display
- CSS `perspective` with `rotateY()` transforms create a gallery-hall depth illusion
- Previous and next artworks are visible at the sides — rotated, smaller, and dimmed — simulating peripheral vision in a real gallery
- **Ken Burns effect**: current artwork slowly and continuously zooms (CSS `@keyframes`) like a documentary camera movement
- **Spotlight effect**: CSS radial gradient above the current artwork simulates a ceiling exhibition spotlight
- **Frame simulation**: multi-layer `box-shadow` mimics a gilt picture frame with shadow depth

### 🔊 AI Narration (Text-to-Speech)
- Uses the **Web Speech API** (`SpeechSynthesisUtterance`) — no external API, no cost, works offline
- Each artwork has a hand-crafted 100–150 word expert narration script covering:
  - Historical context and biography
  - Technical analysis (medium, style, technique)
  - Emotional and symbolic interpretation
  - Art-historical significance
- **Voice selection algorithm**: prefers warm English voices (Samantha, Karen, Daniel) with fallback to any en-GB or en-US voice
- **Rate and pitch tuned** (`rate: 0.88`, `pitch: 1.05`) for a measured, authoritative gallery-guide tone
- **Narration bar**: slides up from the bottom showing the full transcript while speech plays

### 🎧 Guided Tour Mode
- Auto-plays narration for each artwork
- Waits for narration to complete (`utterance.onend`) then automatically advances to the next artwork
- Tour badge shows current position (`3 / 8`)
- Stop tour at any time — narration halts cleanly

### 📐 Canvas Image Zoom & Pan
- Artwork detail opens in a modal with a **Canvas 2D** rendering
- **Scroll wheel**: zoom from 0.5× to 8× using scale transform
- **Click and drag**: pan the image at any zoom level
- **Touch drag**: mobile pan support via `touchmove` events
- **Double-click**: resets to fit-in-view (scale=1, pan=0)
- Image is drawn centered with `ctx.translate()` + `ctx.scale()` on every frame

### 🎵 Ambient Gallery Audio (Web Audio API)
- Procedurally generated — no audio files required, no external dependencies
- Creates a **5-harmonic drone** at fundamental frequencies: 55 Hz, 110 Hz, 165 Hz, 220 Hz, 330 Hz
- Each harmonic goes through a **BiquadFilter** (low-pass, 350 Hz cutoff) to soften the texture
- **Gain envelope**: fades in over 2.5 seconds (`linearRampToValueAtTime`) for a smooth appearance
- **Fade out**: same technique when toggled off — no abrupt clicks
- Evokes the soft, reverent ambiance of a museum gallery

### 🧭 Navigation
| Input | Action |
|---|---|
| `←` `→` Arrow keys | Navigate between artworks |
| `Space` | Play AI narration for current artwork |
| `Enter` | Open detail modal |
| `Escape` | Close modal or stop narration |
| Click side artwork | Jump to that artwork |
| Room dots | Jump to any artwork |
| Swipe (planned) | Mobile gesture navigation |

---

## Artwork Collection

| # | Title | Artist | Year |
|---|---|---|---|
| 1 | The Starry Night | Vincent van Gogh | 1889 |
| 2 | Girl with a Pearl Earring | Johannes Vermeer | 1665 |
| 3 | The Great Wave | Katsushika Hokusai | 1831 |
| 4 | The Persistence of Memory | Salvador Dalí | 1931 |
| 5 | The Birth of Venus | Sandro Botticelli | 1485 |
| 6 | The Scream | Edvard Munch | 1893 |
| 7 | A Sunday Afternoon (La Grande Jatte) | Georges Seurat | 1886 |
| 8 | Mona Lisa | Leonardo da Vinci | 1517 |

All artworks are public domain, sourced from Wikimedia Commons.

---

## Technical Knowledge Implemented

### Browser APIs
| API | Usage |
|---|---|
| **Web Speech API** (`SpeechSynthesis`) | Text-to-speech narration — voice selection, rate, pitch, onend callback |
| **Web Audio API** | Ambient drone — OscillatorNode, BiquadFilterNode, GainNode, linearRampToValueAtTime |
| **HTML5 Canvas 2D** | Image zoom/pan — ctx.translate(), ctx.scale(), ctx.drawImage(), wheel/drag events |
| **CSS 3D Transforms** | Gallery depth — perspective, rotateY(), translateZ(), preserve-3d |
| **CSS Animations** | Ken Burns effect — @keyframes kenburns with scale/translate |
| **CSS Backdrop-filter** | Header/narration bar blur glass effect |

### JavaScript Patterns
| Pattern | Implementation |
|---|---|
| **Circular index navigation** | `(idx + dir + n) % n` — wraps from last to first artwork |
| **Utterance lifecycle** | `onend` callback to chain guided-tour advancement |
| **Voice matching** | Regex test on voice name + language fallback chain |
| **Gain ramp envelope** | `linearRampToValueAtTime` for click-free audio fade |
| **Canvas transform stack** | `ctx.save()` / `ctx.translate()` / `ctx.scale()` / `ctx.restore()` |
| **Touch event handling** | `touchstart` + `touchmove` delta for mobile pan |
| **CSS class toggle** | All UI states (modal, narration bar, tour badge) driven by class add/remove |

### CSS Architecture
| Technique | Application |
|---|---|
| CSS Custom Properties | `--gold`, `--bg`, `--frame`, `--text` color tokens |
| CSS Perspective | `perspective: 900px` + `rotateY()` for 3D side panels |
| CSS Animations | Ken Burns (scale/translate), blinking narration dot |
| CSS Backdrop-filter | Frosted glass header and narration bar |
| Multi-layer box-shadow | Simulated gilt picture frame with depth |
| Radial gradient | Ceiling spotlight on current artwork |
| CSS Grid | Modal layout (image / info side by side) |

---

## Narration Design — AI Curator Voice

Each artwork's narration script is structured as expert gallery commentary:

```
1. Opening orientation  — "Standing before [Title], we witness…"
2. Historical context   — When, where, circumstances of creation
3. Technical analysis   — Medium, technique, specific visual elements
4. Symbolic reading     — What the imagery means, iconography
5. Art-historical impact — Why this work matters in the canon
6. Closing invitation   — Draw the visitor in to look more carefully
```

This structure mirrors the format used by audio guides at institutions like the Louvre, MoMA, and the National Gallery.

---

## Planned Enhancements (Future Roadmap)

| Feature | Description |
|---|---|
| **3D Walkthrough** | First-person camera movement through a CSS/Three.js rendered hall |
| **Dynamic Artwork Upload** | Visitors upload their own artwork + AI generates narration via LLM API |
| **Image Enhancement Filters** | Canvas-based filters: edge detection (Sobel), color analysis, brush-stroke detection |
| **Multi-language Narration** | Language selector → TTS narration in French, German, Japanese |
| **Room Collections** | Separate gallery wings: Impressionism, Surrealism, Photography, Sculpture |
| **Art Style Analysis** | In-browser ML (TensorFlow.js) classifies uploaded artwork by style/period |
| **Virtual Tour Sharing** | URL hash encodes current artwork — shareable deep links |
| **VR Mode** | WebXR API for head-mounted display support |
| **Social Annotations** | Visitor comments anchored to specific regions of artworks |

---

## How to Run

```bash
# Open directly — no server required
open index.html

# Or serve locally for CORS-safe image loading
python3 -m http.server 8080
# → http://localhost:8080
```

GitHub Pages deployment: push to `main` branch → enable Pages → live at `https://[username].github.io/virtual-art-gallery`

---

## Skills Demonstrated

`Web Speech API (TTS)` · `Web Audio API` · `HTML5 Canvas 2D` · `CSS 3D Transforms` · `CSS Animations (Ken Burns)` · `Zoom & Pan with Canvas` · `Touch Event Handling` · `Guided Tour State Machine` · `Voice Selection Algorithm` · `Procedural Audio Generation` · `Circular Navigation` · `CSS Glassmorphism` · `Art History Domain Knowledge` · `Responsive Design` · `Single-File Architecture`
