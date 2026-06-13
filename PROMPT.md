Du arbeitest am Projekt **ai-artefacts** – eine Sammlung von KI-generierten HTML/SVG-Visualisierungen.

## Projektstruktur

- **`index.html`** – Navigations-Hub, verlinkt alle Demos und zeigt SVG-Schaltpläne als Vorschau + Modal an
- **`*.html`** – eigenständige interaktive Demos (ESP32 Breadboard, Schaltpläne, etc.)
- **`*.svg`** – Schaltplan-Dateien (optional, können auch inline in `index.html` eingebettet sein)

## Artefakt-Typen

### 1. Interaktive Demo (`.html` Datei)
Erstelle die HTML-Datei als vollständig eigenständige Seite. Danach füge in `index.html` einen Eintrag in der `.demos-grid` hinzu:

```html
<a href="deine_datei.html" class="card">
  <div class="card-icon">📊</div>
  <div class="card-title">Titel der Demo</div>
  <div class="card-desc">Kurzbeschreibung der Funktionalität</div>
</a>
```

### 2. SVG-Schaltplan
Erstelle die SVG-Datei (oder generiere sie direkt). Danach sind **zwei** Stellen in `index.html` zu aktualisieren:

**a) HTML-Eintrag** (in `.svg-gallery`):
```html
<div class="svg-item" data-i="N" onclick="openModal(this)">
  <div class="svg-preview"></div>
  <div class="svg-name">Name des Schaltplans</div>
</div>
```
Dabei ist `N` der nächste freie Index.

**b) SVG-String** im `<script>`-Block am Ende der Datei:
```js
var SVGS = [
  // ...vorhandene SVGs...
  `<svg ... width="800" height="600">
     ... SVG-Inhalt ...
   </svg>`
];
```
Wichtig: Das SVG muss explizite `width` und `height` Attribute haben (z. B. `width="800" height="600"`), sonst wird es im Modal nicht korrekt skaliert.

## Nach Änderungen

1. Lokal testen: `open index.html`
2. Commit und Push:
   ```
   git add .
   git commit -m "Beschreibung der Änderung"
   git push
   ```
