# Implementation Info — jQuery Toggle & Button Click (`subscribe.html`)

Lab write-up for the two jQuery effects added to the Advisory Subscription page.

---

## 1. Aim

To implement, on the Heatwave portal's subscription page:

1. **Toggle hide/show** of a message element (`#message`)
2. **Button click event** that shows an alert when the user clicks a button (`#btn`)

using jQuery, as described in the class notes.

---

## 2. Where it lives

| What | File | Location |
|---|---|---|
| jQuery library include | `subscribe.html` | `<head>`, line 12 |
| HTML elements (`#btn`, `#toggleBtn`, `#message`) | `subscribe.html` | inside `<main>`, lines 65–77 (the *Alert message preview* section) |
| jQuery event handlers | `subscribe.html` | `<script>` just before `</body>`, lines 83–89 |

`subscribe.html` is the **only page** in the portal that loads jQuery — every
other page uses native JavaScript.

---

## 3. Implementation

### 3.1 Loading jQuery

```html
<script src="https://code.jquery.com/jquery-3.7.1.min.js"></script>
```

- Placed in `<head>` so `$` is defined before the page's own script runs.
- jQuery 3.7.1 is fetched from the official CDN (content delivery network),
  so no local copy of the library is needed.

### 3.2 HTML elements

```html
<p>
  <button id="btn" type="button">Click Me</button>
  <button id="toggleBtn" type="button">Hide / show message</button>
</p>
<div id="message" class="block sev-mod">
  <p><strong>Sample advisory:</strong> Stay indoors between 12 noon and 4 pm,
     drink at least 3 L of water a day and check on elderly neighbours.</p>
</div>
```

| Element | id | Role |
|---|---|---|
| Button | `btn` | Fires the alert (click event demo) |
| Button | `toggleBtn` | Triggers the hide/show toggle |
| `<div>` | `message` | The advisory box that is hidden/shown; styled `.block sev-mod` (yellow severity block from `style.css`) |

`type="button"` stops the buttons from behaving like submit buttons inside the
form area — they only run the script.

### 3.3 jQuery event handlers

```js
$("#btn").click(function () {
  alert("Button clicked!");
});

$("#toggleBtn").click(function () {
  $("#message").toggle();
});
```

**Line-by-line**

| Line | Meaning |
|---|---|
| `$("#btn")` | Select the element whose id is `btn` (jQuery id selector) |
| `.click(function () { … })` | Attach a function that runs every time that button is clicked |
| `alert("Button clicked!")` | Browser popup — proof that the event fired |
| `$("#message")` | Select the message `<div>` |
| `.toggle()` | If the element is **visible → hide** it; if it is **hidden → show** it |

---

## 4. How `toggle()` works

`toggle()` flips the CSS `display` property between `none` and the element's
default display value (`block` for a `<div>`):

| State before click | `toggle()` action | State after click |
|---|---|---|
| visible (`display: block`) | hides | `display: none` |
| hidden (`display: none`) | shows | `display: block` |

**Equivalent plain JavaScript (what jQuery does internally):**

```js
var message = document.getElementById("message");

if (message.style.display === "none") {
  message.style.display = "block";
} else {
  message.style.display = "none";
}
```

**Equivalent click handler in plain JavaScript:**

```js
document.getElementById("btn").addEventListener("click", function () {
  alert("Button clicked!");
});
```

---

## 5. Event flow (what happens in the browser)

**Click Me**

```
user click
  → browser fires "click" event on #btn
  → jQuery's .click() handler runs
  → alert("Button clicked!") opens a popup
  → user presses OK, popup closes
```

**Hide / show message**

```
user click
  → browser fires "click" event on #toggleBtn
  → jQuery's .click() handler runs
  → $("#message").toggle() checks current display value
  → sets display:none  (if it was visible)
     or display:block  (if it was hidden)
  → the advisory box disappears / reappears instantly
```

---

## 6. Testing / demo steps

1. Open `subscribe.html` (server or double-click; **internet required** for jQuery).
2. Press **Click Me** → alert box appears with *"Button clicked!"* → OK.
3. Press **Hide / show message** → the yellow advisory box disappears.
4. Press it again → the box reappears. Repeat — state flips every time.
5. DevTools (F12) → Console: no errors. If jQuery failed to load the console
   shows `$ is not defined` and both buttons stay dead.

---

## 7. JS vs jQuery (for the viva)

| Feature | JavaScript | jQuery |
|---|---|---|
| Select element | `document.getElementById("message")` | `$("#message")` |
| Toggle hide/show | read `style.display`, assign `"none"` / `"block"` | `$("#message").toggle()` |
| Button click | `document.getElementById("btn").addEventListener("click", fn)` | `$("#btn").click(fn)` |
| Lines of code | ~7 | ~3 |
| External library | none | jQuery (CDN) |

**Why this matters:** jQuery selects elements with the `#id` syntax, chains
methods, and wraps repeated DOM tasks (`addEventListener`, `display`
juggling) into single, shorter methods like `.click()` and `.toggle()`.

---

## 8. Limitations & precautions

- **Internet required** — jQuery comes from `code.jquery.com`; offline the
  buttons do nothing (`$ is not defined` in the console).
- **Only this page uses jQuery** — the rest of the portal is dependency-free,
  so the project still works offline everywhere else.
- **`type="button"`** prevents accidental form submission when the demo
  buttons are placed near the subscription form.
- **Single-element selectors** — `#btn`, `#toggleBtn` and `#message` are used
  once each; ids must stay unique in the page.

---

## 9. Conclusion

The subscription page now demonstrates both jQuery event concepts from the
class notes — **`.click()`** for event handling and **`.toggle()`** for
hide/show — in about six lines of code, while keeping the portal's existing
colour scheme (`.sev-mod` block) and layout unchanged. The side-by-side
plain-JavaScript equivalents show exactly what jQuery abstracts away.
