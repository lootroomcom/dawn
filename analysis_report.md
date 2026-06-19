# Analyse des Themes auf Hardcoding und Best-Practice-Empfehlungen

Diese Analyse untersucht die vorliegende Codebase (ein Shopify-Theme, das auf dem Dawn-Framework basiert) auf statisch hinterlegte (hardcodierte) Werte. Hardcoding kann die Wartbarkeit, Skalierbarkeit und Anpassbarkeit eines Themes für Händler erheblich erschweren, da Änderungen tiefgreifende Eingriffe in den Code erfordern.

Im Folgenden finden Sie einen Bericht über die gefundenen Stellen, kategorisiert nach Problemfeldern, sowie entsprechende Best-Practice-Lösungsvorschläge. Da es sich um eine reine Analyse handelt, wurden keine Dateien verändert.

---

## 1. Farben und Design-Werte

### Gefundene Beispiele:
*   In der Datei `sections/chic-multi-columns.liquid` (Zeilen 138-144) sind in den Schema-Defaults feste HEX-Codes hinterlegt (z. B. `#ffffff`, `#111111`, `#ff00cc`, `#333399`). Dies ist an sich noch kein Hardcoding im Code, führt aber oft dazu.
*   In `layout/theme.liquid` und `sections/main-product.liquid` existieren Inline-Styles mit `rgba()` oder `rgb()`-Werten:
    *   `<span class="svg-wrapper" style="color: rgb(238, 148, 65)">`
    *   `<span class="svg-wrapper" style="color: rgb(62, 214, 96)">`
*   Vielfache Nutzung von `#fff` oder `black` in `assets/base.css` und `assets/side-by-side-slider.liquid`.

### Problematik:
Wenn Farben direkt im Code oder als Inline-Style vorgegeben werden, greifen globale Theme-Einstellungen (Color Schemes) nicht mehr. Ein Händler kann die Farbe nicht über den Shopify Theme-Editor anpassen.

### Best-Practice-Empfehlung:
1.  **Shopify Color Schemes nutzen:** Das Dawn-Theme bietet mit Online Store 2.0 ein mächtiges Farbschema-System. Anstatt Farben fest zu definieren, sollten HTML-Elementen Klassen wie `color-{{ section.settings.color_scheme }}` zugewiesen werden.
2.  **CSS-Variablen anstelle von festen Werten:** Verwenden Sie globale CSS-Variablen wie `var(--color-button-text)` oder weisen Sie diese lokal über Inline-Styles zu, wenn sie aus Settings kommen: `style="--sbs-text: {{ section.settings.text_color | default: settings.colors_text }};"`.
3.  **Vermeidung von Inline-Styles:** Komplexe Styles sollten in eigene CSS-Dateien ausgelagert werden.

---

## 2. Abmessungen, Abstände und Breakpoints

### Gefundene Beispiele:
*   Massive Verwendung von festen Pixelwerten (`px`) in der `assets/base.css` (z. B. `@media screen and (min-width: 750px)` oder `height: 20px`).
*   In Schema-Dateien (z. B. `sections/main-page.liquid`, `sections/collage.liquid`, `sections/multirow.liquid`) sind Abstände in `px` (`"unit": "px"`) fest als Einstellungen kodiert.

### Problematik:
Feste Pixelwerte skalieren nicht gut, besonders wenn Benutzer die Standardschriftgröße im Browser ändern (Barrierefreiheit / Accessibility). Wenn zu viele Abstände im CSS verankert sind, bricht das Layout auf bestimmten Bildschirmgrößen schneller.

### Best-Practice-Empfehlung:
1.  **Relative Einheiten verwenden:** Nutzen Sie `rem` oder `em` für Schriftgrößen, Abstände (Margin/Padding) und Breite/Höhe. Eine Daumenregel lautet: `1rem = 10px` (bei entsprechender Basis-Definition im `html`-Tag).
2.  **Theme-Settings für Struktur:** Shopify 2.0 erlaubt es, strukturelle Abstände (Spacing) global über die Theme-Einstellungen zu regeln (z. B. `settings.spacing_grid_horizontal`). Dies verhindert Inkonsistenzen.

---

## 3. Externe Links, Assets und URLs

### Gefundene Beispiele:
*   In `sections/collage.liquid` und `sections/video.liquid` sind Standard-URLs für YouTube/Vimeo fest verankert:
    *   `src="https://www.youtube.com/embed/..."`
    *   `"default": "https://www.youtube.com/watch?v=_9VUPq3SxOc"`
*   In `sections/side-by-side-slider.liquid` werden externe Bibliotheken direkt über CDNs geladen:
    *   `<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/swiper@11/swiper-bundle.min.css">`
    *   `<script src="https://cdn.jsdelivr.net/npm/swiper@11/swiper-bundle.min.js" defer></script>`
*   In `templates/index.json` ist ein Bildpfad hart auf das Shopify-CDN referenziert: `https://cdn.shopify.com/s/files/...`

### Problematik:
*   **Datenschutz (DSGVO):** Das direkte Laden von Assets über externe CDNs (wie jsdelivr) kann ohne User-Consent problematisch sein, da IP-Adressen an Dritte übertragen werden.
*   **Wartbarkeit:** Wenn sich die URL ändert, eine Datei vom CDN gelöscht wird oder der Händler das Bild tauschen möchte, bricht die Funktionalität oder erfordert Code-Eingriffe. JSON-Templates mit statischen Bild-URLs sind besonders fragil.

### Best-Practice-Empfehlung:
1.  **Lokales Hosting von Assets:** Externe Bibliotheken (wie Swiper.js) sollten in den `assets/`-Ordner des Themes heruntergeladen und lokal eingebunden werden (`{{ 'swiper-bundle.min.css' | asset_url | stylesheet_tag }}`).
2.  **Section Settings für Medien:** Verlinkungen auf Bilder, Videos oder externe Seiten sollten *immer* als Section/Block Settings im Schema angelegt werden. So kann der Händler sie im Visual Editor verwalten.
3.  **JSON-Templates bereinigen:** Im `index.json` sollten statt hartkodierter URLs dynamische Referenzen auf Dateien im Shopify-Admin-Bereich oder leere Platzhalter (die dann im Editor gefüllt werden) verwendet werden.

---

## 4. Texte und Inhalte

### Gefundene Beispiele:
*   In `sections/footer-group.json` (bzw. dem Footer) ist spezifischer Text fest codiert:
    *   `"text": "<p>lootroom GmbH is officially affiliated...</p><p>© 2026 Facepunch Studios Limited...</p>"`

### Problematik:
Wenn Texte direkt in den Liquid- oder JSON-Dateien des Themes stehen, können sie nicht von Händlern bearbeitet werden, ohne in den Code einzugreifen. Zudem ist das Theme dadurch nicht übersetzbar (mehrsprachige Shops).

### Best-Practice-Empfehlung:
1.  **Shopify Translations:** Alle Benutzeroberflächen-Texte (Buttons, Formular-Labels, Fehlermeldungen) müssen über das Locale-System (`locales/*.json`) referenziert werden (`{{ 'path.to.string' | t }}`).
2.  **Inhalte über den Editor pflegen:** Markenspezifische Rechtstexte im Footer sollten über einen `richtext`-Block in der Footer-Section eingepflegt werden. Der Händler gibt den Text im Editor ein, das Theme rendert nur `{{ block.settings.text }}`.

---

## Zusammenfassung und Handlungsempfehlung

Das Theme weist an mehreren Stellen typische Muster von "Quick-and-Dirty"-Entwicklung auf, bei denen Werte (Farben, URLs, spezifische Texte) hartkodiert wurden, anstatt die dafür vorgesehenen Schnittstellen (Theme-Settings, Sections-Schema, Asset-Ordner, Locales) zu nutzen.

**Nächste Schritte für eine Überarbeitung (Refactoring):**
1.  Identifikation aller externen CDN-Skripte/CSS und Migration in den `assets/` Ordner.
2.  Ersetzung aller festen Farben (z.B. in Inline-Styles) durch Shopify Color Schemes oder CSS Custom Properties (`var(...)`), welche an die Theme-Einstellungen geknüpft sind.
3.  Austausch harter Bild-Links in JSON-Templates und Sections gegen File-Picker in den Schema-Einstellungen.
4.  Überführung aller hartkodierten redaktionellen Texte in `locales` oder Section-Settings.