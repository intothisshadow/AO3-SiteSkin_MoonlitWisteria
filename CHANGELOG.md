# Moonlit Wisteria — Changelog

The changelog documents significant changes only — new features, architectural decisions, and notable bug fixes. Individual CSS tweaks, rule refinements, and minor mobile adjustments are not logged.



## 🔧 v1.0.2 (current) — 2026-05-14 — Dashboard, Filter Panel & Legend Fix Pass



### Desktop (`style_desktop.css`)

**Fix 1 — Dashboard `.current` tab higher than siblings**
The base button selector (`.actions a, button, .current, …`) gives all matched elements `position: relative`, which caused the active dashboard link (`span.current`) to render taller than its `display: block` neighbours. Added `position: static !important` to the `div#dashboard .current` / `#dashboard .current` rule so the active item stays flush. Also updated the rule comment to document the reason.

**Fix 2 — Navigation `.current` tab (Works page, etc.) cut off**
Removing `.current` from the base button selector (attempted in a prior pass) broke the active tab on works/bookmarks/etc. pages because `span.current` lost all its layout properties (`display: inline-block`, `height`, `padding`, `border-radius`, etc.) and rendered as a bare inline element. Reverted: `.current` stays in the base button selector for layout. The active-state gradient rule (`a:active, .current, a.current, …`) was also restored to its original form.

**Fix 3 — Fieldset legend gap**
The fieldset `legend` was set to `position: static` but still occupied block height (browser renders a static legend as in-flow content). Fixed by fully zeroing the legend — `height: 0; font-size: 0; opacity: 0; margin: 0; padding: 0` — matching AO3's own default behaviour for hidden legends.

**Fix 4 — Visible legends on `form.verbose` / `form.single` / `.authentication`**
The zero-height legend rule used `fieldset:not(…)×5 legend`, giving it specificity `(0,5,2)`. A restore rule at lower specificity (`form.verbose legend` = `(0,1,2)`) could never win, so Post New Work, Edit Work, Preferences, and login form legends remained invisible. Fixed by adding `form.verbose`, `.verbose`, `form.single`, and `.authentication` fieldsets directly to the `:not()` exclusion chain on the zero-height rule, so those legends are never touched by it. A restore block below then applies the standard gradient pill styling; `.authentication fieldset legend` further down overrides with its distinct `violet → mist-blue` gradient.

**Fix 5 — Dead code removal**

- Removed stale `/* Fix 6: reduced from 1.5em top padding — legend is now static… */` comment (legend is now zeroed, not static-positioned).
- Removed `fieldset fieldset legend` rule (gradient applied to a legend that's always invisible — never rendered).
- Stripped dead properties from the zero-height legend rule (`border-radius`, `box-shadow`, `color`, `left`, `letter-spacing`, `top` — all no-ops at `height:0; opacity:0`).
- Updated `.authentication legend` selector to `.authentication fieldset legend` for correct specificity.

**Fix  6 — Verbose form legends invisible:** replaced complex `:not()` legend-hide with plain `legend {}` global hide; patched `#inbox-form fieldset.actions legend` to include `opacity: 1; height: auto`

---

### Mobile (`style_mobile.css`)

**Fix 1 — Filter panel only half-visible**
The mobile filter panel had only `overflow-y: auto` and a background colour — no positioning. The panel was rendering in-flow and clipping. Replaced with the full fixed-overlay implementation: `position: fixed; right: -100vw; height: 100%; width: 100vw; top: 0; bottom: 0; z-index: 400; transition: right 0.25s ease`, with `.filtering form.filters { right: 0 }` and `.filtering { right: 0 }` for the JS open state. Also added the margin-top cancellation block (`.javascript form.filters fieldset, dl, p { margin-top: 0 }`) and `form.filters legend { display: none }` to suppress the partially-hidden legend AO3 leaves in the panel.

**Fix 2 — Dashboard `.current` tab higher than siblings (mobile)**
Added a scoped `div#dashboard .current / #dashboard .current` mobile rule with `display: inline-block; position: static; vertical-align: middle; padding: 2px 8px` — matching the inline-block layout of sibling dashboard links on mobile and cancelling `position: relative` from the base button block.

**Fix 3 — `.current` restored in mobile button overrides**
The mobile button override block (font-size/padding resize) was missing `.current` after a prior edit. Re-added to keep the active tab consistently sized with other buttons at mobile scale.

**Fix  4 — Download dropdown cut off on mobile**: reset `position: static` + `width: 100%` on `.javascript .work.navigation li` and `.secondary` within `≤ 62em`; `display: block` only on `.expanded+.secondary` to avoid overriding `.hidden`

---

## 🔧 v1.0.1 — 2026-05-11 — Mobile & Layout Fix Pass



### Desktop (`style_desktop.css`)

**Fix 1 — `#main` box model**
Added a new standalone `#main { box-sizing: border-box }` block. Wisteria had no such block; AO3 default leaves `#main` as `content-box`, which causes padding and border to inflate the element beyond its container on narrower viewports.

**Fix 2 — `#main.dashboard` margin**
No change required. Wisteria's desktop CSS had no explicit margin override on `#main.dashboard`, so AO3's default `margin: 0.5em auto 1em 14em` was already operative. (Pop2Ko had a conflicting override that needed removal; Wisteria did not.)

**Fix 3 — `#dashboard` selector coverage**
Already correct. The existing rule targets `div#dashboard, #dashboard` with no `.own` qualifier, so it applies to all dashboard pages. No change needed.

**Fix 4 — `#dashboard` box-sizing + explicit px width**
Added `box-sizing: border-box` and `width: 210px` to the `div#dashboard, #dashboard` block. The px width keeps the sidebar comfortably inside AO3's `14em` margin-left slot (14em × 20px base = 280px maximum; 210px leaves 70px clearance).

**Fix 5 — `#dashboard a` text alignment**
Added `text-align: left` to the `div#dashboard a / #dashboard a` block. AO3 default sets `#dashboard ul { text-align: right }`, which right-aligns all sidebar links unless explicitly overridden per-link.

**Fix 6 — Fieldset `legend` position**
- Changed `legend` from `position: absolute; top: -0.4em; left: 1em` to `position: static; top: auto; left: auto`. The absolute-positioned "pill straddling the border" effect causes layout issues in many AO3 form contexts.
- Removed `position: relative` from the fieldset container (it was only needed to anchor the absolute legend).
- Reduced fieldset top padding from `1.5em 1.2em 1em` to `1em 1.2em` (the large top padding had been compensating for the overlapping pill; it's no longer needed).

---

### Mobile (`style_mobile.css`)

**Fix 1 — Media query wrap**
Already correct. The existing file properly wraps content in `@media only screen and (max-width: 62em)` with `:root` outside. No change needed.

**Fix 2 — `#outer` background override**
Added `background-color: #F5EDE8` as a solid-colour fallback alongside the existing gradient. AO3's narrow CSS resets `#outer { background: #fff }`, which was overriding the Wisteria gradient on mobile. The explicit `background-color` overrides the AO3 reset.

**Fix 3 — `border-image: none` removal**
Not present in Wisteria's mobile CSS. No-op. (Wisteria desktop has no border-image on `#main` or `#dashboard`, unlike Pop2Ko, so there was nothing to strip.)

**Fix 4 — `#main` mobile block expansion**
The existing `#main, #main.dashboard` mobile block only had `margin-left: 0; width: 100%`. Expanded to include: `box-sizing: border-box`, `float: none`, `margin: 0`, `max-width: 100%`, `min-width: 0`, `padding: 0.5em 0.5em 2em`. These resets ensure the element doesn't overflow or float on any dashboard page.

**Fix 5 — `form.filters { width: 100% }` removed**
Removed the entire `form.filters` block (`max-width: 100%; min-width: 0; width: 100%`). AO3 handles filter form width correctly on its own; this rule was redundant and interfered with the JS slide-in panel behaviour.

**Fix 6 — `.javascript form.filters` panel styling**
Added new block: gradient + solid-colour background, `box-shadow`, `max-height: 100vh`, `overflow-y: auto`, `padding: 0.5em`. This ensures the slide-in filter panel has a visible, scrollable, styled background on mobile rather than appearing as a transparent overlay. Background colours are hex/rgba literals (no `var()` inside gradient — AO3 validator requirement).

**Fix 7 — `overflow-x: hidden` moved from `body` to `#outer`**
Removed `overflow-x: hidden` from the `html, body` block; added it to `#outer`. Applying `overflow-x: hidden` to `body` can suppress `position: sticky` behaviour and interfere with scroll in nested elements. Scoping it to `#outer` contains horizontal overflow without those side effects.

**Fix 8 — `.userstuff blockquote` indent**
Added `margin-inline-start: 0.25em`. Browser default is `40px`, which causes blockquotes to overflow `#main` on narrow screens.

**Fix 9 — Guideline/faq/docs notice/caution/example box margins**
Added `margin-left: 0.25em; margin-right: 0.25em` to `.guideline .userstuff .notice`, `.caution`, `.example` and equivalent `.faq` and `.docs` selectors. Default margins cause these boxes to overflow the content column on small viewports.

**Fix 10 — `.userstuff table` scroll**
Added `display: block; width: 100%; overflow-x: auto`. Wide tables in user-content areas (FAQ, guidelines) previously caused full-page horizontal scroll.

**Fix 11 — Docs/guideline/faq `ul`/`ol` tightening**
Added `padding-left: 0.75em; margin: 0.3em 0` to list elements in `.docs`, `.guideline`, `.faq` contexts. Browser default `padding-left: 40px` causes lists to overflow on narrow screens.

**Fix 12 — Docs/guideline/faq `li` padding**
Reduced list item padding to `0 0.375em` in `.docs`, `.guideline`, `.faq` contexts.

**Fix 13 — Guideline/docs `h2.heading` truncation**
Added `font-size: 0.85em; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; width: 100%; box-sizing: border-box` to `#main.faq`, `.docs`, `.guideline`, `.support`, `.donate` `h2.heading`. Long section headings on these pages were wrapping and pushing content below the visible area.

**Fix 14 — Fieldset `margin-top`**
Added `fieldset { margin-top: 0.75em }` in the ≤ 42em block. Fieldsets were sitting flush against the element above them on mobile with no breathing room.

---

### Dashboard mobile block — consolidated

The old `#dashboard, #dashboard.own` mobile block (which only had `border-radius: 0; margin-bottom: 0.5em`) was replaced with a full layout reset block covering:
- `box-sizing: border-box`
- `float: none`
- `margin: 0 0 0.5em 0`
- `max-width: 100%; min-width: 0`
- `padding: 4px`
- `position: static`
- `width: 100%`

Also added `div#dashboard ul.navigation.actions li { display: inline-block }` and `#dashboard a { display: inline-block; font-size: 0.8em; padding: 2px 8px; white-space: nowrap }` for compact horizontal link layout on mobile. The `#dashboard .landmark { clear: both }` rule was kept and moved into this consolidated block.

---

## 🌸 v1.0.0 — 2026-03-12 — Initial Release

First public release of Moonlit Wisteria, a soft wisteria and rose gradient site skin for AO3.
