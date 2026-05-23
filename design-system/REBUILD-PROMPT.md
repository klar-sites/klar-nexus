# Task: Rebuild a site's visual design as a standalone, token-driven design system

## Source and output location

- **Source (read-only):** the existing website app in `{{SOURCE_DIR}}` — when run from
  inside a local app, this is the directory I'm currently in. (It can also be a URL or
  screenshots of a site I found.) Study its pages, markup, scripts, images, content,
  and existing CSS. **Never modify the source directory.**
- **Output (write here):** create the replica in a separate directory,
  `{{TARGET_DIR}}` (new or empty). **All deliverable paths below are relative to this
  target directory root**, not the source.
- **`{{TARGET_DIR}}` must be an absolute path. In local-directory mode `{{SOURCE_DIR}}`
  must also be absolute and the target must live outside the source tree (a sibling
  directory, never nested inside it).**

Reconstruct the source's **visual design language** from scratch as a self-contained
design system. Do **not** reuse the source's CSS, framework, class names, or markup —
study how it looks (colors, type, spacing, radii, shadows, components, layout) and
rebuild it cleanly. The original stylesheet must be entirely discarded; nothing in the
output may depend on it (no Tailwind/Bootstrap/etc. classes, no leftover source
classes, no second stylesheet).

## Deliverables

1. `style.css` — one standalone stylesheet at the target directory root. No imports except a
   web-font `@import`/`<link>` if needed. It is the single source of truth; the
   whole site can be built from its classes alone.
2. `design-system/design-system.html` — a living style guide that renders every
   token and component live from `/style.css`.
3. **The site itself** — every page of the target, **re-skinned** onto the new design
   system: keep the source's markup structure, scripts, images, content, and behavior,
   and change only the stylesheet link and the class names so each page is styled
   **only** by `style.css` (the same components, section patterns, classes, and tokens
   demonstrated in `design-system.html`). Do not regenerate pages from scratch or drop
   anything — see "Scope of change" below.

## `style.css` requirements

Author it tokens-first, in this order, with section banners as comments:

1. **Design tokens** in `:root`:
   - Raw palette (e.g. `--color-black`, `--color-gray-100`…) extracted from the target.
   - **Semantic** tokens mapped onto the palette: `--background`, `--foreground`,
     `--primary`, `--primary-foreground`, `--secondary`, `--muted`,
     `--muted-foreground`, `--accent`, `--destructive`, `--border`, `--input`,
     `--ring`, `--card`, etc.
   - Typography: `--font-sans` (match the target's typeface), a type scale
     `--text-xs … --text-5xl`, font weights, line-heights, letter-spacing.
   - Spacing: a 4px-based scale `--space-0 … --space-24`.
   - Layout (`--content-width`, `--header-height`), radii (`--radius-sm/md/lg/full`),
     shadows (`--shadow-sm/md/lg`), motion (`--transition*`).
2. **Reset & base** — box-sizing, margins, base typography on `html/body/h1–h6/a/img`.
3. **Layout primitives** — `.container`, `.section`, etc.
4. **Components** — semantic, BEM-ish classes for everything the target shows:
   buttons (`.btn`, `.btn-primary`, `.btn-outline`, sizes…), `.card` (+ `__header`/
   `__title`/`__body`/`__footer`), `.badge`, `.tag`, `.input`/`.textarea`/`.select`/
   `.label`/`.field`, `.site-header`/`.brand`/`.nav-link`, `.hero`, `.cta`,
   `.site-footer`/`.footer-link`, and any others the site needs. Components consume
   the tokens — never hardcoded values.
5. **Utility classes** — token-mapped, Tailwind-like names (`.bg-primary`,
   `.text-muted-foreground`, `.flex`, `.gap-6`, `.p-8`, `.text-2xl`, `.rounded-lg`,
   `.shadow-md`, `.grid-cols-3`…). Every utility you reference in the HTML must exist
   in the CSS — no phantom classes.
6. **Responsive breakpoints** — `sm/md/lg` escaped variants (`.md\:grid-cols-3`).
7. **Dark theme** — a `.dark` block that overrides only the semantic color tokens, so
   theming is automatic everywhere.

## `design-system/design-system.html` requirements

- Links `/style.css`. The **only** inline `<style>` allowed is documentation chrome,
  every rule prefixed `.ds-` and clearly commented "not part of the design system."
  No component styling lives here.
- Structure:
  - A sticky header + hero **built from the system's own components/tokens** (so the
    page showcases the system while looking finished).
  - **Foundations** sections: Colors (swatches), Typography (type scale + weights),
    Spacing, Radius & Elevation — rendered from the real tokens.
  - **Component Library**: one subsection per component group. Wrap each group in a
    listing container carrying `data-klar-ds-elements="GroupName"` (e.g. `Buttons`,
    `Badges`, `Cards`, `Forms`). Components sit directly inside, showing every variant
    and state.
  - **Sections**: full page-pattern variants (header, hero, post/content grid, CTA,
    contact, footer…) inside a `data-klar-ds-elements="Sections"` container, where
    **each direct child is a self-contained root section** that could be extracted on
    its own.
- A small theme-toggle script wiring `[aria-label="Toggle theme"]` to toggle `.dark`.
- Mobile-responsive and accessible (labels, `aria-*`, focus states, alt text).

### Reference structure to follow closely

Design the actual look, tokens, components, and content **freely** from the target —
but keep the document architecture and the documentation-chrome (`.ds-*`) conventions
close to this skeleton:

```
<html>
  <head>
    <link rel="stylesheet" href="/style.css" />
    <style> /* documentation chrome ONLY — every rule prefixed .ds-, commented
               "not part of the design system": .ds-page, .ds-block, .ds-note,
               .ds-subhead, .ds-specimens(--grid|--stack), .ds-sections,
               .ds-swatches/.ds-swatch, .ds-type-row, .ds-scale, .ds-radius-row,
               .ds-shadow-row, .ds-showcase-hero(+__inner/__badge/__title/__lead/
               __actions/__stats) */ </style>
  </head>
  <body>
    <!-- sticky nav, built from the system's OWN components (.site-header, .brand,
         .nav-link, .btn) — section anchors + a theme-toggle button -->
    <header class="site-header">…</header>

    <!-- showcase hero (doc chrome): eyebrow/badge, big gradient title, lead,
         CTA buttons, a stat row -->
    <section class="ds-showcase-hero" id="top">…</section>

    <main class="container ds-page">
      <!-- FOUNDATIONS — one .ds-block per topic, each: <h2> + <p class="ds-note"> -->
      <section class="ds-block" id="colors">      … .ds-swatches / .ds-swatch … </section>
      <section class="ds-block" id="typography">  … .ds-type-row rows + weights … </section>
      <section class="ds-block" id="spacing">     … .ds-scale … </section>
      <section class="ds-block" id="radius">      … .ds-radius-row + .ds-shadow-row … </section>

      <!-- COMPONENT LIBRARY — each group: <h3 class="ds-subhead"> + a listing
           container with data-klar-ds-elements="GroupName" holding the variants -->
      <section class="ds-block" id="components">
        <h3 class="ds-subhead">Buttons</h3>
        <div class="ds-specimens" data-klar-ds-elements="Buttons"> … </div>
        … Badges, Tags, Forms (.ds-specimens--grid), Cards, … …
      </section>

      <!-- SECTIONS — full page patterns; each DIRECT CHILD is a self-contained
           root section (extractable on its own) -->
      <section class="ds-block" id="sections">
        <div class="ds-specimens ds-specimens--stack ds-sections"
             data-klar-ds-elements="Sections">
          <header class="site-header">…</header>
          <section>…hero…</section>
          <section>…content/post grid…</section>
          <section>…CTA…</section>
          <section>…contact…</section>
          <footer class="site-footer">…</footer>
        </div>
      </section>
    </main>

    <script> /* theme toggle: [aria-label="Toggle theme"] → toggle .dark */ </script>
  </body>
</html>
```

Treat this as the shape to match, not markup to copy — the components, swatches,
type specimens, and sections inside must reflect the **target's** design.

## Rebuilt site requirements

- Recreate every page of the source (`index.html` and the rest) at the target directory
  root, each linking **only** `/style.css` — no other stylesheet, no inline component
  styles.
- **Bring the source's scripts, images, fonts, and other assets into the target
  unchanged**, preserving their relative paths and `src`/`href` references, so the
  re-skinned pages keep working — **local-directory mode: copy** them from the source;
  **URL mode: download** them. The only thing you do not carry over is the source's
  stylesheet (it's replaced by the new `style.css`).
- Build each page purely by assembling the design system's components and the
  self-contained section patterns from the `Sections` block of `design-system.html`.
  If a page needs something the system doesn't have, add it to `style.css` and the
  design system first, then use it — never style ad hoc in the page.
- Match the target's content, page structure, navigation, and routing faithfully; only
  the underlying CSS and class names change to the new system.
- Every class used in a page must exist in `style.css`; no source-site classes or
  markup patterns may remain.

## Scope of change — what changes vs. what stays exactly as before

The **only** things that change are the styling layer and the added design system:

- **Changes:** the stylesheet (the source's CSS is fully replaced by the new
  `style.css`), the class names in the markup (rewired to the new system's classes),
  and the new `design-system/design-system.html`.
- **Stays exactly as in the source — do not alter, rewrite, regenerate, or drop:**
  - All JavaScript / `<script>` files and inline scripts, and their behavior.
  - All images and other assets, and their file paths / `src`/`href` references.
  - All text content and copy, the full set of pages, navigation, routing, and
    `<head>` metadata.
- **Preserve every script/integration hook**: keep existing `id`s, `data-*`
  attributes, ARIA attributes, form `name`s, and any element the scripts query or the
  CMS/integration relies on. When markup must change to adopt a component's structure,
  keep those hooks intact so behavior is byte-for-byte identical.
- Net effect: the site is re-skinned onto the new design system but works exactly as
  before — same scripts, same images, same content, same behavior. Only the look
  (CSS + class names) and the added design-system page are new.

## Hard constraints

- Use **only** the new design system's class names and tokens — in both files.
- The source site's stylesheet/framework is removed; output depends on nothing but
  `style.css`.
- No hardcoded colors/sizes in components or HTML — reference tokens/utilities.
- `style.css` is fully standalone; `design-system.html` works by loading just it.

## Process

1. Analyze the target's design language (palette, type, spacing rhythm, radii,
   shadows, component inventory, section patterns, light/dark).
2. Write `style.css` tokens-first, then components, then utilities, then dark theme.
3. Build `design-system/design-system.html` to exercise every token, component,
   variant, and section — using only the new classes.
4. Re-skin the site's pages onto the design system — swap the stylesheet link and
   class names to the new system, leaving scripts, images, content, structure, and
   script hooks untouched (see "Scope of change").
5. Self-check: open the design system and the re-skinned pages, confirm light/dark
   both render correctly, no invisible text, no class referenced in any HTML that's
   missing from CSS, every page depends only on `style.css`, no source-site classes or
   stylesheet remain — and that all original scripts, images, content, and behavior
   are still present and working.
