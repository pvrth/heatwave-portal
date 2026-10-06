# HeatWatch — Heatwave Intelligence System

A static HTML + CSS + JavaScript portal for heatwave monitoring, alerts and advisories.
Built for the **Web Development Laboratory (Semester III, AY 2026–27)**.

No build tools, no server-side code — open the files and they run.
The only library used is **jQuery**, loaded on `subscribe.html` only, for the
toggle-hide/show and button-click demo (see §6, "jQuery demo").

---

## 1. How to run the project

**Option A — simplest:** double-click `index.html`. It opens in the browser.

**Option B — local server (recommended for demos):**

```bash
cd heatwave-portal
python3 -m http.server 8000
# then open http://localhost:8000
```

**Option C — VS Code:** install *Live Server* → right-click `index.html` → *Open with Live Server*.

> Keep the browser console open during the demo: **F12 → Console tab**.
> Several programs print their output there (`console.log`).

---

## 2. File map

### Pages (20 HTML files)

| File | What it is |
|---|---|
| `index.html` | Home — hero, active alert banner, three link cards |
| `alert-details.html` | Severe heatwave bulletin (banner, split layout, `<abbr>`) |
| `about-system.html` | Objective / data sources / stakeholders blocks + blockquote |
| `sources.html` | Data sources, workflow `<ol>`, stakeholders, abbreviations `<dl>` |
| `hotspot-map.html` | Image map (`<map>`/`<area>`) linking to the two region pages |
| `region-north.html` | North Zone detail (extreme severity block) |
| `region-central.html` | Central Zone detail (high severity block) |
| `forecast.html` | Forecast table with `rowspan`, `colspan`, `<thead>/<tfoot>` |
| `subscribe.html` | Advisory subscription form (radio + checkbox fieldsets) + **jQuery toggle/click demo** |
| `media.html` | Video, audio, Google Maps iframe, `<noscript>` warning |
| `navigation.html` | Site map, external link, contact links |
| `heatwave-form.html` | **Set A – Task 1** monitoring form (pure HTML5) |
| `heatwave-validation.html` | **Set A – Tasks 2–5** same form + JavaScript validation |
| `programs.html` | **Set B hub** — links to all six JavaScript programs |
| `heatwave_analysis.html` | **Set B – Task 1** variables, operators, message printing |
| `heatwave_warning.html` | **Set B – Task 2** if / else-if / else decision |
| `climate_object.html` | **Set B – Task 3** object literal with methods and `this` |
| `temperature_analysis.html` | **Set B – Task 4** array + loops analysis |
| `climate_validation.html` | **Set B – Task 5** regex validation of registration details |
| `climate_registration.html` | **Set B – Task 6** full registration form, 15 rules |

### Assets

- `style.css` — the entire design system (one stylesheet, ~107 lines)
- `images/` — `logo.svg`, `hero.svg`, `map.svg`, `station.svg`, `satellite.svg`
- `media/advisory.wav` — audio clip used on `media.html`
- `README.md` — this file

---

## 3. Design system (style.css)

Everything visual comes from one file. Quote these during the viva.

### Colour tokens (`:root`, style.css:1-4)

```css
--ink:#1f2933;  --muted:#5f6b7a;  --rule:#d5dae1;  --bg:#f3f4f6;
--navy:#12355b; --navy-dark:#0c2540;   /* header, nav, footer, buttons */
--red:#b42318;  --amber:#f2b441;  --green:#3f7d35;
```

Colours are never hard-coded twice — always `var(--navy)` etc.

### Severity palette (used on blocks, tables, decisions)

| Class | Meaning | Background |
|---|---|---|
| `.sev-low` | Normal / no alert | light green |
| `.sev-mod` | Moderate | light yellow |
| `.sev-high` | High | light orange |
| `.sev-ext` | Extreme | light red |

### Key layout classes

| Class | Purpose |
|---|---|
| `.topbar`, `.site-header`, `.brand`, `.site-nav`, `.site-footer` | Shared page shell |
| `.hero` | 2-column home hero (collapses to 1 column ≤760px) |
| `.cols` | Auto-fit responsive card grid |
| `.split` | 1.3fr / 1fr content + image split |
| `.block` + `.sev-*` | Coloured severity block with left border |
| `.notice` / `.warn` | Red alert banner / yellow warning banner |
| `.table-wrap` | Horizontal scroll wrapper for wide tables |
| `.field`, `fieldset`, `legend` | Form grouping (from `subscribe.html`) |
| `.error`, `.result`, `.invalid` | Validation messages + red field highlight |
| `.legend` | Map colour key (`--c` custom property per swatch) |

### Typography & responsive

- Google Fonts: **Source Sans 3** (body) + **Source Serif 4** (headings)
- `html{font-size:17px}` — all sizes in `rem`
- `:focus-visible{outline:3px solid var(--amber)}` — keyboard focus ring
- One media query: `@media (max-width:760px)` stacks `.hero` and `.split`

---

## 4. The shared page shell

Every page uses the same skeleton — this is a talking point for "how pages connect":

```html
<div class="topbar">…helpline…</div>
<header class="site-header">…brand/logo…</header>
<nav class="site-nav" aria-label="Main">
  <ul>…12 links, one has aria-current="page"…</ul>
</nav>
<main> …page content… </main>
<footer class="site-footer">…</footer>
```

- The nav has **12 items**: Home · Alert · About · Sources · Map · Forecast ·
  Subscribe · Media · Form · Validation · Programs · Navigation
- Exactly **one** link carries `aria-current="page"` — that is what paints the
  amber underline (`.site-nav a[aria-current="page"]`, style.css:29).
- `region-north/central` are reached from the image map, so they show no
  current marker (they are sub-pages of Map).
- Adding a nav item means editing **all 20 files** (copy-paste line, change
  `aria-current`). The nav sits on one line: `flex-wrap:nowrap` + `overflow-x:auto`
  (style.css:26).

---

## 5. Task Set A — the form (Tasks 1–5)

### Task 1 → `heatwave-form.html` (HTML only)

- **9 fields**: Observer Name, Email, Mobile, AWS Station ID, Location,
  Observation Date, Maximum Temperature, Humidity, Alert Level
- **HTML5 input types**: `text`, `email`, `tel`, `date`, `number` (`step="0.1"`),
  plus `<select>` for the alert level
- Every field wrapped in `<div class="field">` with a `<label for="…">`
- Three `<fieldset>` + `<legend>` groups: Observer Details, Station Details,
  Observation Data
- All fields carry the `required` attribute → browser blocks empty submits
- Submit posts to `#` (demo only — no server)

### Tasks 2–5 → `heatwave-validation.html` (HTML + JS)

The script (bottom of the file) is the examinable part:

**Helper**
```js
function showError(id, message) {
  document.getElementById(id + "Error").textContent = message;
  return message === "";   // true = field is valid
}
```
Each input has a sibling `<span class="error" id="fieldError">`.

**Task 2 — Name & Station**
```js
/^[A-Za-z ]+$/      // name: alphabets + spaces, minimum 3 characters
/^AWS[0-9]{3}$/     // station: AWS001, AWS123 …
```

**Task 3 — Email & Mobile**
```js
/^[^\s@]+@[^\s@]+\.[^\s@]+$/   // email
/^[6-9][0-9]{9}$/              // 10-digit Indian mobile (starts 6–9)
```

**Task 4 — Weather data** (`validateTemp`, `validateHumidity`, `validateDate`)
- temperature: non-empty → `isNaN` check → range −10 … 60 °C
- humidity: range 0 … 100 %
- date: must not be empty

**Task 5 — Alert level + submit**
```js
form.addEventListener("submit", function (event) {
  event.preventDefault();                     // page does NOT reload
  var results = [validateName(), validateEmail(), …, validateLevel()];
  var allValid = results.every(function (r) { return r === true; });
  if (allValid) result.textContent = "Thank you, " + name + "…";
});
```
- Runs **every** validator first so all error messages appear together
- Success message only when all nine checks return `true`
- A `reset` listener clears all messages

---

## 6. Task Set B — six JavaScript programs

Open them from **`programs.html`** (nav item *Programs*).

### Program 1 — `heatwave_analysis.html` (Task 1)
Concepts: variables, data types, arithmetic operators, expressions, conditionals,
`alert()`, `document.write()`, `console.log()`.

- Declares `locationName`, `currentTemperature = 39`, `heatwaveThreshold = 37`,
  `humidity`, `monitoringDate`
- `temperatureDifference = currentTemperature - heatwaveThreshold` → **2**
- Status via ternary → *Heatwave Condition Detected*
- Prints the same five lines **three ways**: `console.log`, `document.write`
  (styled `.block` injected into the page while it parses), and one `alert()`

### Program 2 — `heatwave_warning.html` (Task 2)
Concepts: `if / else if / else`, relational (`>`, `<`, `>=`, `<=`) and logical
(`&&`, `||`) operators.

Rules are checked **in the order given in the assignment**:

```
temp >= 40                       → SEVERE  – Severe Heatwave Warning
temp >= 38 && humidity >= 60     → HIGH    – Issue Heatwave Warning
temp 35…38                       → MODERATE – Monitor Conditions
forecast >= 40                   → EARLY WARNING – Prepare
otherwise                        → NORMAL  – Continue Monitoring
```
Sample input `Pune, 39 °C, 65 %` produces **HIGH / Issue Heatwave Warning** —
matches the expected output. Early-warning text uses `||` and a nested ternary.
The rule table is also rendered as an HTML `<table>` below the result.

### Program 3 — `climate_object.html` (Task 3)
Concepts: object literal, properties, methods, `this`, dot operator.

- `climateData` holds location, city, temperature, humidity, windSpeed,
  forecastTemperature, threshold, riskLevel, alertStatus, monitoringDate
- Five methods: `displayData()`, `getTemperatureDifference()`,
  `determineRiskLevel()`, `getEarlyWarningStatus()`, `updateAlertStatus(newStatus)`
- Every method reads properties with `this.` (e.g. `return this.temperature - this.threshold`)
- `determineRiskLevel()` uses conditionals and **reassigns** `this.riskLevel`
- Demo of mutation: `updateAlertStatus("Escalated")` → page shows `Active → Escalated`

### Program 4 — `temperature_analysis.html` (Task 4)
Concepts: arrays, `for` loop traversal, conditionals, arithmetic, `push`, `join`.

```js
let temperatureReadings = [34,36,38,39,41,40,37,35,39,42];
```
One loop computes min **34**, max **42**, sum → average **38.1**,
count ≥ 40 °C → **3**, and collects `heatwaveReadings` → **41, 40, 42**.
Counts and average are rounded with `Math.round(x*10)/10`.

### Program 5 — `climate_validation.html` (Task 5)
Concepts: strings, `test()` with regular expressions, functions, conditionals.

```js
/^[A-Za-z ]+$/        // user name  (Rahul Sharma)
/^[6-9][0-9]{9}$/     // mobile     (9876543210)
/^[^\s@]+@[^\s@]+\.[^\s@]+$/  // email
/^[A-Za-z ]+$/        // location
/^CLM-[0-9]{4}$/      // user ID    (CLM-1234)
/^[0-9]{6}$/          // PIN        (400077)
```
Same `showError` pattern as Set A: field-level messages, `preventDefault()`,
success message only when all six validators pass.

### Program 6 — `climate_registration.html` (Task 6)
Concepts: DOM access, functions, regex, events, conditional logic, highlighting.

- **10 fields**: name, mobile, email, user ID, city, PIN, age group, user
  category (7 options), preferred alert channel, registration date
- Rule highlights:
  - errors appear **beside** the field (`.error` span)
  - invalid fields are **highlighted** (`classList.add("invalid")` → red border
    from style.css)
  - **future dates blocked** — `value > todayString()` compares `YYYY-MM-DD`
    strings, and `input.max = today` disables them in the date picker
  - form cannot submit until all ten checks pass (`preventDefault`)
  - success text is exactly:
    *Registration Successful! You are registered for Climate Intelligence
    Heatwave Monitoring and Early-Warning Alerts.*
- `reset` clears messages **and** removes `.invalid` classes

### jQuery demo — `subscribe.html`

> Full lab write-up (aim, code, event flow, testing, JS vs jQuery):
> see **[JQUERY-IMPLEMENTATION.md](JQUERY-IMPLEMENTATION.md)**.

Loads jQuery from the CDN (in the `<head>` of `subscribe.html`) and runs two
handlers in the same style as the class notes:

```html
<button id="btn">Click Me</button>
<button id="toggleBtn">Hide / show message</button>
<div id="message" class="block sev-mod">Sample advisory …</div>
```

```js
$("#btn").click(function () {
  alert("Button clicked!");
});

$("#toggleBtn").click(function () {
  $("#message").toggle();     // hides if visible, shows if hidden
});
```

Compare with the plain-JavaScript version during the viva:

| Feature | JavaScript | jQuery |
|---|---|---|
| Toggle hide/show | read `message.style.display`, set `"none"` / `"block"` | `$("#message").toggle()` |
| Button click | `document.getElementById("btn").addEventListener("click", …)` | `$("#btn").click(…)` |

Every other page in the portal uses **native JavaScript only** — `subscribe.html`
is the only page that loads jQuery.

---

## 7. Demo script for the viva (≈10 minutes)

1. **Home page** — brand, topbar helpline, amber current-page underline.
2. **Colour system** — open `style.css:1-4`, explain tokens + severity classes.
3. **`heatwave-form.html`** — point out input types, labels, fieldsets, `required`.
   Submit empty → browser blocks it (native validation).
4. **`heatwave-validation.html`** — F12 Console.
   - Submit empty → all nine messages appear at once
   - Type `John3` for name → "letters and spaces only"
   - Station `ABC123` → "must look like AWS001"
   - Mobile `1234567890` → must start 6–9
   - Fill everything correctly → green success message
5. **Hotspot map** — clickable `<map>` areas → region pages → back to map.
6. **Forecast table** — `rowspan`/`colspan`, severity-coloured cells.
7. **Programs hub** → run each:
   - *Analysis*: dismiss the `alert()`, then show the same lines in the Console
   - *Warning*: explain the rule order → HIGH recommendation
   - *Object*: show `this.` methods and `Active → Escalated`
   - *Array*: min/max/average/counts with the loop
   - *Validation*: trigger each regex error
   - *Registration*: future date, `.invalid` red highlight, success banner
8. **Subscribe page (jQuery)** — press *Click Me* → alert; press
   *Hide / show message* twice → the advisory box toggles. Show the two-line
   jQuery code and the JS-vs-jQuery table in §6.
9. **Media page** — audio player, `<noscript>` fallback, iframe.

---

## 8. How to add a new page (checklist)

1. Copy any existing page as the template (shell is identical).
2. Set `<title>` and the `aria-current="page"` marker to the new nav link.
3. Insert the new `<li>` into the nav of **all 20 files**
   (before the `Navigation` item), e.g.
   ```html
   <li><a href="newpage.html">New</a></li>
   ```
4. Add it to the link list in `navigation.html` (the site map).
5. Link it from `programs.html` or a relevant card if it is a sub-page.
6. Check: every internal `href` resolves, exactly one `aria-current` per page.

---

## 9. Good practices already in the project (say these in the viva)

- Semantic HTML: `header`, `nav`, `main`, `footer`, `section`, `blockquote`, `cite`, `dl`
- Accessibility: `aria-label` on nav, `aria-current` for the active page,
  `alt` text on every image, `title` on the iframe, amber `:focus-visible` ring,
  `<noscript>` warning, `lang="en"`, viewport meta
- External link uses `target="_blank" rel="noopener noreferrer"`
- All data is clearly labelled **sample data** in the footer
- One stylesheet, custom properties instead of repeated colour literals,
  mobile breakpoint at 760 px
- Validation runs fully client-side — no backend required

---

## 10. Troubleshooting

| Symptom | Fix |
|---|---|
| Fonts look generic | Internet needed for Google Fonts; fallbacks (Segoe UI/Georgia) appear offline |
| No `console.log` output visible | Open DevTools → Console tab (F12) |
| `alert()` doesn't show | Pop-ups may be blocked for the tab — allow them |
| Video won't play | `media.html` uses an external MDN sample URL; needs internet |
| Nav items wrap | They should not — `style.css:26` uses `nowrap`; check the file wasn't edited |
| Form "does nothing" on submit | Expected: `preventDefault()` + success message below the buttons |
| Toggle / Click Me button dead on `subscribe.html` | jQuery comes from `code.jquery.com` — internet required; check the CDN script tag and the Console for `$ is not defined` |
