# Dominik Maslan — Personal Website

Static personal website for Dominik Maslan (Growth Architect). No build tools, no dependencies — plain HTML, CSS, and vanilla JavaScript. Deployed automatically via Vercel on every push to `main`.

---

## Project structure

```
personal-web/
├── index.html                        # Homepage
├── blog.html                         # Blog index
└── blog/
    └── programming-languages.html    # Article: 7 Programming Languages
```

Every page is a self-contained HTML file with all CSS and JavaScript inlined. There is no package.json, no bundler, no framework.

---

## Pages

### `index.html` — Homepage

The main landing page. Sections (in order):

| Section | ID | Description |
|---|---|---|
| Navigation | `#navbar` | Fixed nav with logo, links, EN/CZ toggle |
| Hero | `#hero` | Headline, sub-copy, CTA buttons, stat card |
| About | `#about` | Bio paragraphs, career timeline, Core Philosophy card |
| Expertise | `#expertise` | Three skill cards + data literacy banner |
| Contact | `#contact` | Contact form (demo) + contact info |
| Footer | — | Copyright + nav links |

### `blog.html` — Blog index

Grid of article cards. Currently contains one live article and two "coming soon" placeholder cards (rendered at 45% opacity).

### `blog/programming-languages.html` — Article

Interactive article about 7 programming languages. Features a sticky tab bar (HTML → CSS → JavaScript → React → Next.js → Python → SQL) that switches the visible language panel without a page reload. Each panel contains a role badge, description, type/difficulty/runs-in badges, "when to use" tags, and a syntax-highlighted code example.

---

## Features

### Design system

All design tokens are defined as CSS custom properties in `:root` on each page:

```css
:root {
  --bg:       #0d0d0f;   /* page background */
  --surface:  #141418;   /* card / input background */
  --border:   #232329;   /* borders and dividers */
  --accent:   #00d0d0;   /* primary teal accent */
  --accent-2: #5ee8e8;   /* lighter teal for text/tags */
  --text:     #e8e8f0;   /* primary text */
  --muted:    #7b7b8f;   /* secondary text */
  --max-w:    1100px;    /* content max-width */
  --radius:   14px;      /* card border-radius */
}
```

To change the accent colour site-wide, update `--accent` and `--accent-2` in all three files (and any hardcoded `rgba(0,208,208,…)` alpha values).

### Language switcher (EN / CZ)

Every page has an EN | CZ toggle in the nav bar. It works with a `data-i18n` attribute system — no page reload needed.

**How it works:**

1. Each translatable element carries `data-i18n="key"` (for HTML content) or `data-i18n-placeholder="key"` (for input placeholder attributes).
2. A `T` object in each page's `<script>` block holds `{ en: { key: value }, cs: { key: value } }`.
3. `setLocale(loc)` iterates all annotated elements and sets `innerHTML` / `placeholder`.
4. The chosen locale is saved to `localStorage` under the key `locale` and restored on every page load.
5. `document.documentElement.lang` is updated for accessibility.

**To add a translation key:**

```html
<!-- HTML -->
<p data-i18n="my-key">Default English text</p>
```

```js
// In T object
en: { 'my-key': 'Default English text' },
cs: { 'my-key': 'Český překlad' },
```

**Translation rules:**  
Standard professional/technical terms are intentionally kept in English even in the Czech version: *Growth Architect, performance marketing, brand strategy, data literacy, SEO, PPC, RTB, CMO, SaaS, full-funnel, landing page, positioning, analytics, machine learning, full-stack, framework.*

> **Note:** `blog/programming-languages.html` has two separate switchers that must not be confused:
> - `.i18n-btn` / `setLocale()` — the EN/CZ language switcher
> - `.lang-btn` / `switchLang()` — the article's internal HTML/CSS/JS/… tab switcher

### Sticky navigation

The nav starts transparent and gains a frosted-glass background (`backdrop-filter: blur`) once the user scrolls past 40px. Implemented with a `scroll` event listener and a `.scrolled` CSS class.

### Contact form

The form is a front-end demo only — it simulates a submission with a 900 ms timeout, then hides the button and shows a success message. To wire it to a real backend, replace the `handleSubmit` function in `index.html`.

### Syntax highlighting

Code blocks in the programming languages article use hand-crafted CSS classes — no external library. Relevant classes:

| Class | Role |
|---|---|
| `.kw` | Keywords (`function`, `const`, `SELECT`, …) |
| `.fn` | Function names |
| `.str` | Strings |
| `.cm` | Comments |
| `.num` | Numbers |
| `.tag-c` | HTML tags |
| `.attr` | HTML/JSX attributes |
| `.val` | Attribute values |
| `.sel` | CSS selectors |
| `.prop` | CSS properties |
| `.op` | Operators |

---

## How to make common changes

### Change the accent colour

1. Open all three HTML files.
2. In `:root`, update `--accent` and `--accent-2`.
3. Find and replace all hardcoded `rgba(R,G,B,…)` alpha values that reference the old colour.

### Add a new blog article

1. Create `blog/your-article-slug.html`. Use `blog/programming-languages.html` as a template for the page shell (nav, header, footer, i18n setup).
2. Add a card for it in `blog.html` — copy an existing `.blog-card` block and update the `href`, title, description, and meta.
3. Add translations for the new card keys in the `T` object in `blog.html`.

### Add a new translation key

See the [Language switcher](#language-switcher-en--cz) section above.

### Add a new blog section / nav link

1. Add a `<li><a href="…">…</a></li>` to the `<ul class="nav-links">` in every file.
2. Add corresponding `en` and `cs` entries in each page's `T` object, and `data-i18n` on the `<a>` element.
3. Update footer links consistently.

### Modify the contact form

The form handler lives at the bottom `<script>` of `index.html`:

```js
function handleSubmit(e) {
  e.preventDefault();
  // Replace this block with a real fetch() to your backend or a form service
}
```

---

## Deployment

**Stack:** Git → GitHub → Vercel (auto-deploy on push to `main`).

```bash
# Preview changes locally — open any HTML file directly in a browser,
# or serve with any static file server:
npx serve .

# Deploy: just push to main
git add .
git commit -m "Your message"
git push origin main
```

Vercel picks up the push and deploys within ~30 seconds. No build step is required.

**Git author email** must be set to `dominik22maslan@gmail.com` — Vercel rejects commits without it.

```bash
git config user.email "dominik22maslan@gmail.com"
```

---

## Browser support

All features (CSS custom properties, `backdrop-filter`, `clamp()`, CSS Grid, `localStorage`) are supported in all modern browsers. No polyfills are used.
