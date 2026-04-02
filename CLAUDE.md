# CLAUDE.md — Personal Website Project

Project-specific instructions for Claude Code. These complement the global rules in `~/.claude/CLAUDE.md`.

---

## Tech stack

- **Pure static HTML/CSS/JS** — no build tool, no bundler, no framework, no npm
- **Zero external dependencies** — no CDN scripts, no web fonts loaded externally, no analytics SDK
- **Deployment:** push to `main` → Vercel auto-deploys (no build step)
- Every page is a single self-contained `.html` file with all CSS and JS inlined in `<style>` and `<script>` blocks

---

## File structure

```
index.html                       # Homepage (hero, about, expertise, contact)
blog.html                        # Blog index (card grid)
blog/
  programming-languages.html     # Article: 7 programming languages
README.md                        # User-facing project documentation
CLAUDE.md                        # This file
```

---

## Design system

### Colour tokens

Defined in `:root` on every page. **Never hardcode colours outside these tokens** — use `var(--token)`.

| Token | Value | Role |
|---|---|---|
| `--bg` | `#0d0d0f` | Page background |
| `--surface` | `#141418` | Cards, inputs, nav (scrolled) |
| `--border` | `#232329` | All borders and dividers |
| `--accent` | `#00d0d0` | Primary teal — buttons, links, glows, active states |
| `--accent-2` | `#5ee8e8` | Lighter teal — tag text, secondary highlights |
| `--text` | `#e8e8f0` | Primary body text |
| `--muted` | `#7b7b8f` | Secondary text, labels, placeholders |
| `--max-w` | `1100px` | Content max-width |
| `--radius` | `14px` | Card border-radius |

### Accent colour — hardcoded alpha values

Because CSS `rgba()` cannot reference custom properties directly, some alpha tints are hardcoded. The current accent is `#00d0d0` = `rgb(0, 208, 208)`. If the accent ever changes, find and replace all of these:

```
rgba(0,208,208,.06)   rgba(0,208,208,.08)   rgba(0,208,208,.1)
rgba(0,208,208,.12)   rgba(0,208,208,.15)   rgba(0,208,208,.18)
rgba(0,208,208,.2)    rgba(0,208,208,.25)   rgba(0,208,208,.35)
rgba(0,208,208,.4)    rgba(0,208,208,.5)
```

Also update the gradient stops (`#00d0d0 → #5ee8e8 → #a0f0f0`) and the hover/glow colour `#1adede`.

### Gradients

- **Headline gradient:** `linear-gradient(135deg, #00d0d0 0%, #5ee8e8 50%, #a0f0f0 100%)` applied via `-webkit-background-clip: text`
- **Top-border accent:** `linear-gradient(90deg, var(--accent), var(--accent-2))` on philosophy/data-banner cards
- **Hero glow:** `radial-gradient(ellipse, rgba(0,208,208,.18), transparent)` as `::before` pseudo-element

### Typography

- Font stack: `'Inter', 'Segoe UI', system-ui, sans-serif` — system fonts only, no external load
- `h1`: `clamp(42px, 6vw, 74px)`, weight 800, tracking `-.03em`
- `h2`: `clamp(28px, 4vw, 44px)`, weight 800
- Code blocks: `'SF Mono', 'Fira Code', 'Fira Mono', monospace`

### Component patterns

When adding new UI, follow these existing patterns:

- **Cards:** `background: var(--surface)`, `border: 1px solid var(--border)`, `border-radius: var(--radius)`, hover lifts with `transform: translateY(-3px)` and `border-color: rgba(0,208,208,.4)`
- **Tags/pills:** `background: rgba(0,208,208,.12)`, `color: var(--accent-2)`, `border: 1px solid rgba(0,208,208,.25)`, `border-radius: 99px`
- **Section structure:** `<div class="section-header">` containing `.tag`, `h2`, optionally `.section-desc`
- **Buttons:** `.btn.btn-primary` (teal fill) or `.btn.btn-ghost` (transparent + border)

---

## Bilingual setup (EN / CZ)

Every page supports English and Czech switching via `setLocale()` with no page reload.

### How it works

1. Translatable elements use `data-i18n="key"` — `setLocale` sets their `innerHTML`.
2. Input/textarea placeholders use `data-i18n-placeholder="key"` — `setLocale` sets the `placeholder` attribute.
3. The `T` object in each page's `<script>` contains `{ en: { key: value }, cs: { key: value } }`.
4. Active locale is persisted in `localStorage` under the key `locale`.
5. `document.documentElement.lang` is updated on every switch.

### Rules for Czech translations

Keep the following in **English even in the Czech version** — they are established professional/technical terms:

> Growth Architect · performance marketing · brand strategy · data literacy · SEO · PPC · RTB · CMO · SaaS · full-funnel · landing page · positioning · analytics · machine learning · full-stack · framework · dashboard · account management · content strategy · growth loop · attribution · cohort

### Adding a translation key

```html
<!-- 1. Mark the element -->
<p data-i18n="my-key">English fallback</p>
```

```js
// 2. Add to T in the same file's <script>
en: { 'my-key': 'English text' },
cs: { 'my-key': 'Český překlad' },
```

The English value in the HTML is the visible fallback before JS runs — keep it accurate.

### Critical: two separate switchers on the article page

`blog/programming-languages.html` has two independent switching systems. **Do not mix them up.**

| | i18n (language) | Article tabs (programming language) |
|---|---|---|
| Function | `setLocale(loc)` | `switchLang(lang)` |
| Button class | `.i18n-btn` | `.lang-btn` |
| Wrapper class | `.i18n-toggle` | `.lang-nav` |
| Data attribute | `data-locale` | `data-lang` |
| Stored in | `localStorage` | DOM state only |

---

## Page-specific notes

### `index.html`

- The contact form is a **demo only** — `handleSubmit` fakes a 900 ms delay then shows a success message. Do not add real form submission logic without a proper backend or form service.
- The `currentLocale` variable tracks the active locale for use inside `handleSubmit` (to show "Odesílám…" vs "Sending…").
- Skill pills in the expertise cards (PPC / Google Ads, RTB, etc.) are **not translated** — they are technical terms that stay in English.

### `blog.html`

- Placeholder cards (the two "coming soon" cards) use `opacity: .45; cursor: default` inline. Remove these styles when the articles are ready and convert the `<div>` to `<a href="...">`.
- The `cat` (category) span on each card has its own `data-i18n` key because the category label could differ per article.

### `blog/programming-languages.html`

- Language-specific dot colours in the tab bar (`#f07178`, `#264de4`, `#f0db4f`, etc.) are intentional per-language brand colours — do **not** change them to the site accent.
- Syntax highlighting uses hand-written CSS classes (`.kw`, `.fn`, `.str`, `.cm`, `.num`, `.tag-c`, `.attr`, `.val`, `.sel`, `.prop`, `.op`). There is no external highlighting library.
- Code examples use real, runnable code — keep them accurate if modified.

---

## Adding a new blog article

1. Create `blog/your-slug.html`. Copy the full shell from `blog/programming-languages.html` (nav, i18n toggle, footer, `setLocale` script with nav/footer keys). Replace article content.
2. Add a card in `blog.html`: copy an existing `.blog-card` `<a>` block, update `href`, icon, category, `data-i18n` keys for title/desc/meta, and add both `en` and `cs` values to `T`.
3. Remove `opacity:.45; cursor:default` from placeholder cards in `blog.html` if you are replacing one.

---

## Dos and Don'ts

**Do:**
- Keep all CSS and JS inlined in the HTML file — no external `.css` or `.js` files
- Use CSS custom properties for all colours and spacing
- Add `data-i18n` to every piece of user-visible text when adding new content
- Match existing whitespace, indentation (2 spaces), and comment style (`<!-- SECTION -->`)
- Preserve the teal glow/gradient on new accent-coloured elements

**Don't:**
- Don't introduce npm, a bundler, a framework, or any external dependency
- Don't add inline `style` colour values — use tokens or existing utility classes
- Don't create separate `.css` or `.js` files (keep each page self-contained)
- Don't translate skill pills, language names, or technical badge values unless they have a clear Czech equivalent
- Don't modify the programming-language tab colours (`#f07178`, `#264de4`, etc.) — they are intentional language brand colours, not site accent
