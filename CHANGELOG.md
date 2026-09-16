# Changelog — GenCSS for WordPress

Version history for the WordPress plugin. Download any release from the
[Releases page](https://github.com/chrisgoodonline/gencss-wp-releases/releases).

## [0.5.34] — 2026-09-16

Based on GenCSS v1.2.0. Everything here is opt-in — open a stylesheet written
before this release, save it, and you get the same file back.

### Added
- **Content section.** Tables, blockquotes, code and `pre`, horizontal rules,
  figures and captions, definition lists, and inline elements (`sub`, `sup`,
  `abbr`, `mark`, `small`, `kbd`), each with its own controls. Browsers fell back
  to their own defaults for all of these, which ignore every token you have set —
  so a page with a table or a code block stopped looking like the rest of the
  site. Table rows respond to hover, and both the hover and striped colours are
  yours to set
- **Lists section.** Markers for `ul` and `ol`, marker position and colour,
  indent and spacing between items — plus an option that strips markers from
  lists used for layout, which is why bullets kept appearing on grids
- **Header section.** Publishes `--header-height` so anything needing to clear
  the header references one value, with options for sticky, min-height and
  padding. Setting the padding to `0` is the fix for a builder that forces its
  own `<header>` and pads it as well
- **A token picker on every field that takes a token.** A small button opens the
  tokens relevant to that field — colours for a colour field, the spacing scale
  for a spacing field — each with a swatch and the value it resolves to. The
  colour assignment and scheme dropdowns are now fields with the same picker, so
  they show colours rather than a list of names
- **Border width tokens** (`--border-width-light`, `-normal`, `-heavy`) and a
  **smaller spacing step** (`--space-2xs`), for the places `xs` is still too
  generous — compact form fields especially
- **Modern form controls**: fields that size to their content, checkboxes and
  radios that take your accent colour and can be sized, spaced and given a focus
  ring, styled `<select>` dropdowns, and spacing around fields rather than only
  inside them
- **Text box trimming**, so the gap a spacing token asks for is the gap you see
  rather than that gap plus invisible leading

### Changed
- Palettes and tokens you add now appear in the preview
- The **Documentation** button opens the guides on gencss.co.uk

---

## [0.5.33] — 2026-09-08

Based on GenCSS v1.1.0.

### Added
- **Your own colour palettes.** The six palettes were fixed; you can now add more
  with the **+ Add palette** button under the palette list. A new palette is named
  in place and gets everything a built-in gets — the five shades, the transparency
  variants, an optional 50–950 tonal scale, and a place in every token dropdown.
  Renaming one updates any colour assignment pointing at it; deleting one clears
  those assignments rather than leaving them pointing at a colour that no longer
  exists, and tells you how many it will clear first
- **Your own colour assignments.** Layer 2 tokens can be added with **+ Add
  token**, named in place and mapped to any primitive through the same control the
  built-in tokens use. Previously the only place for a custom token was the USER
  block, which meant it sat outside the generated block, had no swatch, and could
  not be chosen in the light and dark scheme dropdowns. Custom tokens now appear
  there like any other

### Upgrading
Existing stylesheets regenerate byte-for-byte identically — palettes and tokens
you have not added cost you nothing.

Palettes and tokens you do add are stored the same way your six built-in palettes
always have been: in the configuration comment at the top of the colour primitives
block. Keep that comment intact — it is the only record of a custom palette's
colour, so deleting it by hand removes the palette.

---

## [0.5.31] — 2026-09-02

Based on GenCSS v1.0.8.

### Fixed
- **Custom CSS in a USER block no longer gains an extra `:root { }` wrapper on every save.** The Forms, Cards and Accessibility sections wrote your custom CSS wrapped in `:root { }` but read it back without unwrapping it, so each save stored the previous save's wrapper as though you had typed it, and the nesting grew by one level for ever. Files saved often had reached ten levels deep. Opening an affected stylesheet and saving once flattens the whole stack back out, with your rules unchanged.
- **Custom properties in a USER block no longer drift two spaces further right on every save.** The same wrapper was removed on read but the indentation it added was not, so the Colour Assignments and Styling Tokens USER blocks crept rightwards indefinitely.
- **A USER block holding a `:root { }` rule followed by another rule is no longer silently corrupted on save.** The wrapper was matched by a pattern that ran to the last closing brace in the block, reshuffling the braces and producing invalid CSS; wrappers are now matched by counting braces, so sibling rules — and a `:root { }` block you wrote yourself — are left alone.
- **Hovering an already-visited link now applies the hover colour.** `a:visited` was emitted after `a:hover` and both carry the same specificity, so for any link the visitor had been to, the visited colour won. Link rules are now emitted in LVHA order.

### Added
- **`:active` (pressed state) controls for links and buttons.** `:active` did not previously exist anywhere in the generator — every state rule was hardcoded as `:hover, :focus-visible` — so a pressed state had to be hand-written in a USER block.
- **Every state property is now definable globally and overridable per variant**, for links and buttons alike: text colour, background, border colour and decoration, across resting, hover and active. This closed several older gaps — link globals had no background or border at any state, and buttons had no global hover row at all.
- An unset state value falls through to the state beneath it rather than to the resting one, so setting only an active background does not snap the text back to its unhovered colour.

### Upgrading
Stylesheets with no state values set regenerate unchanged. Two changes are
intentionally visible on next save: visited links start responding to hover, and
custom CSS in the Forms, Cards and Accessibility USER blocks loses the `:root`
wrapper it had accumulated. That wrapper had been making those rules one point
more specific than written; they still override the generated block above them,
as before.

### Notes
The desktop app's Account modal, added upstream in the same range, is hidden in
the WordPress admin. It releases an Electron device activation through an API the
bridge does not implement, and the plugin has its own licence screen and its own
deactivation route.

---

## [0.5.30] — 2026-08-12

Based on GenCSS v1.0.6.

### Fixed
- **Form fields no longer render with red borders before the user has touched them.** Validation styling used `:invalid`, which matches an empty `required` field the moment the page paints; it now uses `:user-invalid`, which only applies after a blur or a submit attempt. The `.has-error` rule is emitted as a separate block so manual server-side error states keep working on browsers without `:user-invalid` support.
- **Link text-decoration controls now take effect.** The preview scaffold hardcoded an underline that overrode the generated rule, making the Decoration setting look inert.
- **Link hover no longer discards decoration colour and thickness** — the hover rule used the `text-decoration` shorthand, which resets both; it now sets `text-decoration-line`.
- **Typography scale settings are no longer shadowed by a stale cache**, so changes to bases, ratios and breakpoints reliably reach the generated CSS.
- **Form `Border width` now resolves custom properties** — a token entered there was previously emitted raw and produced invalid CSS.

### Added
- **Viewport Breakpoints control** (Layout panel) — the min/max viewport bounding every fluid clamp, shared by the typography and spacing scales. Previously hardcoded to 375–1280px with no way to change it.
- **Fluid small type sizes** — `--text-xxs` and `--text-xs` now scale fluidly instead of holding a fixed value. Off for existing stylesheets, on for new ones; the toggle lives in the Typography panel.
- **Form field line height** control, for aligning inputs, selects and textareas.
- **Per-side form field border widths** (top/right/bottom/left) — set bottom alone with the others at 0 for underline-style fields.

### Upgrading
Existing `globals.css` files regenerate unchanged for the typography scale, field borders and field line height. Two changes are intentionally visible on next save: form fields stop showing error borders until the user interacts with them, and link hover stops resetting decoration colour and thickness.

---

## [0.5.29] — 2026-07-01

### Fixed
- **Card modifier classes now override base card styles.** The base `[class~="card"]:not(...)` selector accumulated specificity from its exclusion chain, preventing modifiers like `.card--dark` from winning; the `:not()` exclusions are now wrapped in `:where()` so modifiers override cleanly by source order.
- **Button variant overrides no longer emit double semicolons** (e.g. `border-radius: var(--radius-pill);;`) when a field value was stored with a trailing `;`.

### Added
- Generated CSS now includes an AI-assistant header comment pointing to GENCSS-AI-GUIDE.md, documenting safe edit paths (Layer 2 remaps, USER blocks) and the calculated values that must not be hand-edited.

---

## [0.5.28] — 2026-06-16

### Fixed
- **Bricks container width now responsive on small screens.** Bricks's generated frontend stylesheet sets a fixed `width` (e.g. `1100px`) on `.brxe-container`, preventing it from shrinking below that value. Added a Bricks compat rule (`width: 100% !important; max-width: var(--container-width) !important`) so containers scale correctly at all viewport sizes.

---

## [0.5.27] — 2026-06-15

### Added
- **Configurable on-surface tonal steps.** Each palette's `on-ld-1`–`on-ld-5` tokens are now user-configurable — the tonal palette card shows light/dark selects for on-surface tokens below the surface rows, under an "on-surface" subheading.

---

## [0.5.26] — 2026-06-15

### Fixed
- **License activation now persists correctly on hosts with aggressive caching** (e.g. Hostinger with LiteSpeed). Three-part fix: `nocache_headers()` on the settings page prevents page-level cache from serving stale license state; `Cache-Control: no-store` on the `/license` REST endpoint prevents response caching; explicit `wp_cache_delete()` after every option write/delete busts any object cache (Redis/Memcached) immediately.

---

## [0.5.25] — 2026-06-14

### Fixed
- **Fluid typography clamps now produce visible size ranges.** Default `desktopBase` raised from `1rem` (16px) to `1.125rem` (18px) and `mobileBase` from `0.9375rem` to `1rem` — the previous defaults produced a 2px base gap that was imperceptible across the type scale.
- **FEWD Studio launch now dismisses the GenCSS welcome screen.** `onLaunchFile` now calls `hideWelcomeScreen()` after connecting, matching every other file-open path.

### Added
- **Custom values in colour scheme tokens.** The light/dark token dropdowns now include a "Custom…" option — type any CSS custom property (e.g. `--primary-ld-5`) instead of being restricted to the predefined assignment list.

---

## [0.5.24] — 2026-06-08

### Added
- **`on-ld` companion tokens for tonal palettes.** Each `--{color}-ld-N` surface token now has a paired `--{color}-on-ld-N` text token that travels in the opposite direction — dark in light mode, light in dark mode. Gives correct contrast for headings and body text placed on a tinted section background without needing `data-theme` workarounds.

---

## [0.5.23] — 2026-06-03

### Fixed
- **globals.css no longer loads in Gutenberg or ACF field contexts.** Removed the `enqueue_block_editor_assets`, `enqueue_block_assets`, `block_editor_settings_all`, and `add_editor_style` hooks that were injecting the design system stylesheet into WP admin editor UI and ACF forms. `globals.css` now loads exclusively on the frontend (and inside Bricks/Etch builder canvas iframes where it is intentionally needed).

---

## [0.5.22] — 2026-06-02

### Fixed
- **Preview CSS now injects reliably on load.** Added a `doc.head` guard to `setLiveCss` so it doesn't fail silently when called while the iframe is mid-navigation. CSS is now injected on both the iframe `load` event and directly via `setLiveCss`, covering the race where `readFile` returns after the iframe has already loaded.

### Added
- **Etch container width integration.** New "Add container rule to globals.css" / "Remove" buttons in Builder Integrations settings. Inserts or removes `[data-etch-element="container"] { width: var(--gccontainer-width); }` in the Layout USER section of `globals.css` so it's editable directly in the CSS editor.

## [0.5.21] — 2026-06-02

### Fixed
- **New preview sections (Reset & Base, tonal swatches) now render correctly.** `demo.html?v=` bypassed the HTML cache but `demo.css` (linked relatively inside `demo.html`) was still served stale. The v1.0.1 CSS classes (`demo-reset-card`, `demo-tonal-sw`, etc.) had no styles, leaving Reset & Base as plain text and tonal swatches as invisible divs. Fix: `loadGlobalsIntoPreview` now also appends `?v=VERSION` to the `demo.css` link, forcing a fresh fetch.

## [0.5.20] — 2026-06-02

### Fixed
- **Iframe cache-buster now applied exactly once.** v0.5.14 added `?v=` to both `$demo_url` and the iframe `src` replacement, producing a double `?v=` that broke iframe loading. v0.5.19 overcorrected by removing it entirely, leaving browsers with a cached old `demo.html`. Fix: `$demo_url` carries no query string; the `?v=VERSION` param is appended once in the iframe replacement.

## [0.5.19] — 2026-06-02

### Fixed
- **Malformed iframe URL on Hostinger resolved.** v0.5.14 accidentally appended `?v=VERSION` in two places, producing `demo.html?v=0.5.19?v=0.5.19` — a broken URL that prevented the preview iframe from loading at all. Removed the duplicate parameter.

## [0.5.18]

### Changed
- Revert to static demo.html — remove endpoint experiments.
