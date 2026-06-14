# Skill-Tree Prompt

Du bist ein erfahrener Frontend-Entwickler. Erstelle eine vollständige, sofort lauffähige Skill-Tree-Anwendung als **einzelne HTML-Datei**.

Technische Grundregel: Alles inline – kein CDN, keine externen Dateien, kein Framework, keine Google Fonts. Die Datei muss offline funktionieren.

---

## DATEN

Ersetze die Beispiel-Einträge durch die echten Aufgaben. Behalte das exakte Format.

```js
const CONFIG = {
  title: "Skill-Tree Titel",       // z. B. "Python Grundkurs"
  subject: "Fach / Klasse",        // z. B. "Informatik – Klasse 9b"
};

const SKILLS = [
  // Startknoten (keine Abhängigkeiten)
  {
    id: "aufgabe-1",
    title: "Titel der Aufgabe",
    description: "Kurze Beschreibung was gelernt/geübt wird.",
    link: "https://...",           // URL zur Übung; null wenn kein Link
    requires: [],                  // leer = Startknoten
    col: 0,                        // Spalte im Grid (0-basiert)
    row: 0,                        // Reihe im Grid (0-basiert)
  },
  // Folgeknoten
  {
    id: "aufgabe-2",
    title: "Titel der Aufgabe",
    description: "Kurze Beschreibung.",
    link: "https://...",
    requires: ["aufgabe-1"],       // IDs der Vorgänger
    col: 0,
    row: 1,
  },
  // Verzweigung
  {
    id: "aufgabe-3",
    title: "Titel der Aufgabe",
    description: "Kurze Beschreibung.",
    link: null,
    requires: ["aufgabe-1"],
    col: 1,
    row: 1,
  },
  // Zusammenführung (mehrere Vorgänger)
  {
    id: "aufgabe-4",
    title: "Titel der Aufgabe",
    description: "Kurze Beschreibung.",
    link: "https://...",
    requires: ["aufgabe-2", "aufgabe-3"],
    col: 0,
    row: 2,
  },
];
```

**Hinweise zum Grid:**
- `col` = horizontale Spalte (0 = ganz links), `row` = vertikale Reihe (0 = ganz oben)
- Knoten gleicher `row` erscheinen auf gleicher Höhe
- Knoten gleicher `col` erscheinen in derselben Spalte
- Überlappungen sind zu vermeiden

---

## Design

**Farbsystem (Dark Mode, nicht verhandelbar):**

| Token              | Wert                    |
| ------------------ | ----------------------- |
| `--bg`             | `#09090f`               |
| `--surface`        | `#111119`               |
| `--surface-raised` | `#18182a`               |
| `--border`         | `#22223a`               |
| `--text`           | `#e4e4f0`               |
| `--text-muted`     | `#5c5c7a`               |
| `--accent`         | `#6366f1`               |
| `--accent-glow`    | `rgba(99,102,241,0.25)` |
| `--success`        | `#22c55e`               |
| `--success-glow`   | `rgba(34,197,94,0.25)`  |
| `--locked`         | `#1e1e2e`               |

**Typografie:** `-apple-system, BlinkMacSystemFont, "Segoe UI", system-ui, sans-serif`
Keine Google Fonts, kein Import.

**Ästhetik:** Professionell wie ein modernes Produktions-Tool (Referenz: Linear, Vercel Dashboard). Kein neon-überladenes Design. Schatten sparsam, Blur subtil.

---

## Knoten-Zustände

### `locked` (gesperrt)
- Hintergrund: `--locked`
- Border: `--border`
- Text: `--text-muted`
- Icon: Schloss als inline-SVG (kein Emoji)
- Cursor: `default`
- Kein Hover-Effekt

### `available` (verfügbar)
- Hintergrund: `--surface-raised`
- Border: `--accent` (1.5px)
- Box-Shadow: `0 0 0 4px var(--accent-glow)`
- Pulsierender Ring: CSS-Animation `pulse` (2s, infinite) – Ring expandiert von border bis ~8px außen und faded aus
- Cursor: `pointer`
- Hover: `translateY(-2px)`, Box-Shadow verstärkt sich

### `completed` (abgeschlossen)
- Hintergrund: `linear-gradient(135deg, #0f2a1a, #0a1f12)`
- Border: `--success` (1.5px)
- Box-Shadow: `0 0 0 4px var(--success-glow)`
- Icon: Checkmark als inline-SVG
- Text: `--text`
- Kein Pulse-Ring

---

## Layout & Canvas

**Knoten-Dimensionen:** 180px × 88px, `border-radius: 10px`

**Grid-Abstände:**
- Horizontal (Spalte zu Spalte, Mitte zu Mitte): 260px
- Vertikal (Reihe zu Reihe, Mitte zu Mitte): 160px
- Canvas-Padding: 60px an allen Seiten

**Verbindungslinien (SVG):**
- Das SVG liegt hinter den Knoten (`z-index: 0`)
- Kurventyp: kubische Bezier-Kurve (vertikal aus dem Knoten heraus, vertikal in den Knoten hinein)
- Farbe gesperrte Verbindung: `--border`
- Farbe freigeschaltete Verbindung: `--accent` mit `opacity: 0.6`
- Farbe abgeschlossene Verbindung: `--success` mit `opacity: 0.7`
- `stroke-width: 2`, `fill: none`, kein Arrow-Head
- Verbindungszustand = Zustand des Ziel-Knotens

**Scrollbarer Container:**
- `overflow: auto` auf dem Canvas-Wrapper
- Canvas-Größe berechnet sich aus max(col) × 260px + 120px Breite, max(row) × 160px + 120px Höhe
- Min-Größe: 100vw, 100vh

---

## Interaktion

### Klick auf `locked`-Knoten
Zeige einen kleinen Tooltip direkt am Knoten (nicht modal):
`"Benötigt: [Titel Vorgänger 1], [Titel Vorgänger 2]"`
Tooltip verschwindet nach 2.5s automatisch.

### Klick auf `available`-Knoten
Öffne ein Modal:
- Titel des Knotens
- Beschreibung
- Wenn `link !== null`: Button `"Übung öffnen →"` (öffnet `link` in `_blank`)
- Button `"Als erledigt markieren ✓"` (primär, Akzentfarbe)
- X-Button oben rechts

### Klick auf `completed`-Knoten
Öffne dasselbe Modal, aber:
- Statt "erledigt markieren": Button `"Zurücksetzen"` (dezent, `--text-muted`-Farbe)
- Zurücksetzen markiert den Knoten als nicht erledigt **und** alle Knoten, die (direkt oder transitiv) von ihm abhängen

### Modal-Details
- Overlay: `rgba(0,0,0,0.7)` mit `backdrop-filter: blur(6px)`
- Karte: `--surface-raised`, max 400px breit, zentriert
- Schließen: ESC-Taste, Klick auf Overlay, X-Button

### Persistenz
- Fortschritt in `localStorage`, Key: `skilltree_v1`
- Wert: JSON-Array der abgeschlossenen Skill-IDs
- Beim Laden der Seite: Zustand wiederherstellen, keine Animation

---

## Animationen

**Beim Abschließen einer Aufgabe:**

1. Knoten-Transformation: `scale(1 → 1.06 → 1)`, Dauer 350ms, `ease-out`
2. Glow-Burst: kurzer Pulse-Ring der aufleuchtet und verschwindet (400ms, einmalig)
3. Partikel: 12 kleine Kreise (4–6px, Farbe `--success`) werden aus dem Knoten-Mittelpunkt in zufällige Richtungen geschossen – per `transform: translate()` animiert (600ms, `ease-out`), danach `opacity: 0`. Implementierung: DOM-Elemente mit `position: fixed`, die nach der Animation entfernt werden (kein Canvas).
4. Neu freigeschaltete Knoten: Fade-in von `opacity: 0` zu `opacity: 1` + `translateY(8px → 0)`, 300ms, mit 200ms Verzögerung nach der Partikel-Animation

**Beim Hover auf `available`-Knoten:**
`translateY(-2px)`, Box-Shadow-Transition, `transition: all 150ms ease`

**Verbindungslinien beim Freischalten:**
Farb-Transition der Linie von `--border` zu `--accent`, 400ms

Alle Transitionen über CSS `transition` oder `@keyframes`. Kein JavaScript-Animation-Framework.

---

## UI-Elemente

**Header (60px, `position: fixed`, `top: 0`, volle Breite):**
- Hintergrund: `rgba(9,9,15,0.85)` + `backdrop-filter: blur(10px)`
- Border-bottom: 1px `--border`
- Links: `CONFIG.title` (16px, semibold) + `CONFIG.subject` (12px, `--text-muted`)
- Rechts: Fortschritts-Text `"5 / 12 erledigt"` + schmale Progress-Bar (4px Höhe, `--accent` als Füllfarbe)
- Canvas-Wrapper hat `padding-top: 60px`

**Reset (im Header, ganz rechts):**
- Kleines Icon-Button mit Pfeil-zurück-SVG, kein Label
- `title="Fortschritt zurücksetzen"`
- Klick → `window.confirm("Gesamten Fortschritt zurücksetzen?")` → bei Bestätigung localStorage leeren + Seite neu laden

---

## Technische Details

- Alle Icons als handgeschriebene inline-SVGs (Schloss, Checkmark, X, Pfeil-zurück) – kein Icon-Font, kein Emoji
- `<meta name="viewport" content="width=device-width, initial-scale=1">`
- `<meta charset="UTF-8">`
- `<title>` aus `CONFIG.title`
- CSS in `<style>` im `<head>`, JS in `<script>` am Ende von `<body>`
- Das JS berechnet aus `SKILLS` automatisch: Knoten-Positionen in Pixel, Canvas-Größe, SVG-Pfade, Abhängigkeitsgraph
- Knoten als `<div class="skill-node" data-id="...">` im Canvas
- Zustandsänderung: CSS-Klassen (`locked` / `available` / `completed`) auf dem Knoten-Element wechseln
- Keine globalen Variablen außer `CONFIG`, `SKILLS` und einer `state`-Variable

---

Generiere jetzt die vollständige HTML-Datei. Füge keine Platzhalter, keine TODOs und keine Kommentare zum Anpassen ein – alle Daten kommen aus dem `SKILLS`-Array oben. Die Datei muss direkt im Browser lauffähig sein.
</parameter>
</invoke>
</function_calls>
<result>
File written successfully.
</result>
</function_calls>

Der Prompt ist fertig unter `/app/skill-tree-prompt.md`. Hier die Übersicht was drin steckt:

---

## Aufbau des Prompts

**DATEN-Abschnitt** (vom User zu befüllen):
- `CONFIG` – Titel und Fach/Klasse
- `SKILLS` – Array mit `id`, `title`, `description`, `link`, `requires[]`, `col`, `row`
- Grid-Koordinaten (`col`/`row`) statt Pixel → einfach und fehlerresistent

**Design-Vorgaben:**
- Vollständiges Dark-Mode Farbsystem als CSS-Tokens
- Klare Ästhetik-Referenz (Linear/Vercel) damit die KI nicht ins Neon-Kitschige abdriftet
- Kein CDN, keine Fonts, keine Frameworks

**Drei Knoten-Zustände:** `locked`, `available`, `completed` – mit exakten visuellen Spezifikationen

**Animationen:**
- Partikel-Burst beim Abschließen (DOM-Elemente, kein Canvas)
- Pulse-Ring auf verfügbaren Knoten
- Freischalten von Nachfolge-Knoten mit Fade-in

**Interaktion:**
- Modal mit Übungs-Link + "Erledigt"-Button
- Tooltip bei gesperrten Knoten zeigt welche Vorgänger fehlen
- Transitives Zurücksetzen (wenn Vorgänger zurückgesetzt → alle Nachfolger auch)
- `localStorage` Persistenz

---

## Workflow für den User

```
1. Prompt kopieren
2. Im DATEN-Abschnitt: CONFIG + SKILLS befüllen
3. An KI senden
4. HTML-Datei erhalten → direkt im Browser öffnen
```

Das Grid-Koordinaten-System (`col`/`row`) ist bewusst simpel gehalten – der User muss kein Layout-Algorithmus-Konzept verstehen, sondern denkt einfach in "Spalte / Reihe".