---
name: wp-navigation-overlay
description: >-
  Generate block markup for a mobile menu, hamburger menu, or nav overlay in
  WordPress — specifically the `navigation-overlay` template part area
  (WordPress 7.0) that opens full-screen from a hamburger button. Use this
  skill whenever the user wants ready-to-paste Gutenberg markup for a
  full-screen WP mobile menu, even if they only describe the look (e.g. "a
  minimal portfolio menu", "a bold editorial menu for a food magazine") or only
  name a business or vibe (bookstore, podcast, vinyl shop, yoga studio,
  brewery, portfolio, magazine). Outputs HTML-comment-delimited block markup,
  no prose. Skip for: CSS-only hamburger icons, React/Vue/native-app menus,
  custom block plugin development, or generic header nav edits.
---

# WordPress Navigation Overlay Markup

This skill generates block markup that the user pastes directly into the WordPress block editor — specifically into the canvas for the `navigation-overlay` template part area. The overlay opens from a hamburger button and renders full-screen on mobile.

## Background

Customisable navigation overlays landed in WordPress 7.0. See the dev note for the full feature context: <https://make.wordpress.org/core/2026/03/04/customisable-navigation-overlays-in-wordpress-7-0/>. Two things from there worth keeping in mind:

- **Overlays are always full-screen.** Sidebar/drawer styles aren't supported yet — don't try to design a partial overlay.
- **Anything goes inside.** "What goes inside an overlay is entirely up to the author" — navigation, social icons, search, site logo, CTAs, images, or any combination. The skill's role is to keep the markup well-formed; the design space is wide open.

## Scope

You're generating the overlay *content* — what lives inside the template part. The editor already provides the `core/template-part` wrapper.

## Hard requirements

These hold for every overlay you produce. The user pastes your output directly into the editor, so a violation means a manual fix on their end.

### Output format

- Return **only** block markup. Start with a top-level `<!-- wp:group -->` (or similar root block) and end with its closing delimiter.
- No prose, no explanation, no markdown code fences around the output. Just the markup.
- No `core/template-part` wrapper.

### Required blocks

- **One** `core/navigation-overlay-close` block, placed in the top-right of the overlay, with generous padding (somewhere in the `var:preset|spacing|30` to `var:preset|spacing|40` range on all sides) so the touch target is comfortable on a phone. (WordPress will auto-insert a fallback close button if one is omitted, but the dev note strongly recommends including your own so it can be styled and positioned intentionally — and so the user isn't surprised by a default-looking button on an otherwise designed overlay.)
- **At least one** `core/navigation` block in vertical orientation (`"layout":{"type":"flex","orientation":"vertical"}`). Set `"overlayMenu":"never"` so the nav doesn't try to nest another overlay inside this one. Use large font sizes — links should be tappable and confident.

### Design tokens only

- Colours: `var:preset|color|…`
- Spacing: `var:preset|spacing|…`
- Font sizes: either the named `fontSize` attribute (`"fontSize":"large"`) or `var:preset|font-size|…` in style values.

Never hardcode hex codes, named CSS colours, or px/em/rem values in style attributes. The one common exception is `minHeight` on `core/cover`, which has no spacing-preset equivalent — a pixel value there is acceptable.

If you don't know what tokens the theme defines, stick to the safe defaults: colours `base` and `contrast`; spacing presets `20`/`30`/`40`/`50`/`60`/`70`/`80`; font sizes `small`/`medium`/`large`/`x-large`/`xx-large`.

### Mobile-first layout

- Design for portrait phone viewports (~375–430px wide).
- Single vertical column, comfortable line heights, plenty of breathing room.
- If content might exceed the viewport, that's fine — the overlay scrolls.

## Positioning rules learned from real outputs

These come from cases where the rendered overlay didn't behave as expected. Follow them unless you have a strong reason not to.

### Keep the outer wrapper minimal

The outermost `<!-- wp:group -->` should be as bare as possible. The template part already provides the full-viewport overlay context — you don't need to recreate it on the outer wrapper.

**Do** start with:

```
<!-- wp:group -->
<div class="wp-block-group">
  ...children...
</div>
<!-- /wp:group -->
```

**Avoid combining these on the outer wrapper:**

- `"minHeight":100,"minHeightUnit":"vh"` — the template part already fills the viewport
- `"backgroundColor":"…"` / `"textColor":"…"` — set these on the template part or on individual sections
- `"layout":{"type":"flex","orientation":"vertical",…}` — flex layouts on a group generate auto-derived classes (`is-layout-flex`, `is-vertical`, `is-content-justification-…`, plus a per-container hash class) that are hard to predict; mismatches trigger "Attempt block recovery"

If you do need padding on the outer wrapper, set `style.spacing.padding` and that's it — leave `layout`, `backgroundColor`, and `minHeight` off the outer block. Push any flex column behaviour onto inner groups that wrap their own content (where the className is simpler to get right).

This rule is conservative — fancier outer wrappers *can* work, but they're a frequent source of validation errors and the visual payoff is usually small.

### Keep the close button at the top level

Place the `core/navigation-overlay-close` block (inside its own right-aligned `core/group` flex row) as a direct child of the outer wrapper — *not* nested inside decorative containers like `core/cover` or any block with custom `contentPosition`.

**Why:** `core/cover` applies `contentPosition` to all its inner content. Even if you put an inner group with `justifyContent:"right"`, the cover's positioning can collapse the flex container's effective width and pin the close button to wherever the cover's content position sits (often bottom-left). The reliable pattern is: close button row first, then any decorative hero block as a separate sibling.

```
<!-- wp:group --> (outer wrapper)
  <!-- wp:group --> (close button row, justifyContent:"right")
    <!-- wp:navigation-overlay-close /-->
  <!-- /wp:group -->
  <!-- wp:cover --> (hero, if any — never wraps the close button)
    ...
  <!-- /wp:cover -->
  ... (rest of overlay)
<!-- /wp:group -->
```

### Prefer `core/image` + `core/group` over `core/cover` for hero sections

`core/cover` background images can fail to render after paste (validation strips the inner img, or the cover's content positioning fights with overlay layout). A `core/group` containing a `core/image` plus separate content is more predictable. Use `core/cover` only when you specifically need overlay text on top of an image with a colour dim.

If you do use `core/cover`, make sure the `url` attribute on the cover matches the `src` on the inner `<img class="wp-block-cover__image-background">` exactly, and include `data-object-fit="cover"` on the img.

### Keep horizontal padding consistent across siblings

A common mistake: text sections are wrapped in inner padded `core/group`s, but an image (or other full-width block) is dropped between them as a direct child of the outer wrapper with no horizontal padding. Result: on mobile, the image goes edge-to-edge while text sits indented — visually disconnected and cramped against the viewport edges.

Pick one approach and stick to it for the whole overlay:

- **Outer wrapper handles padding**: put `style.spacing.padding` on the outer `wp:group` and let children sit naturally inside. Simplest, hardest to get wrong.
- **Per-child padding**: every direct child of the outer wrapper that should respect the content gutter wraps itself in a padded `core/group`. Use the *same* `var:preset|spacing|…` value on every one, including any group that contains an image.

The only blocks that should ever sit at the outer wrapper without horizontal padding are intentional full-bleed elements (like a hero `core/cover` that you specifically want bleeding to the viewport edges).

## Output hygiene to avoid "Attempt block recovery"

WordPress validates that the rendered HTML inside each block matches what the block would produce from its attributes. When it doesn't, the user sees "Block validation failed" and has to click "Attempt block recovery". To minimise this:

- **className strings must reflect every attribute that affects them.** If a `core/group` has `align:"full"`, the rendered div is `class="wp-block-group alignfull"`, not just `wp-block-group`. If a block has `textColor:"base"`, the className must include `has-base-color` and `has-text-color`. Same pattern for `backgroundColor` → `has-X-background-color has-background`. Custom font sizes added via the `fontSize` attribute → `has-X-font-size`.
- **Inline `style` attributes must include every property the block's `style` JSON implies.** If `style.spacing.padding.top` is `"var:preset|spacing|40"` in the attributes, the inline style must have `padding-top:var(--wp--preset--spacing--40)`. Mismatch = validation failure.
- **Self-closing block delimiters take a space:** `<!-- wp:navigation-link {…} /-->`, not `/--}>` or `/-->`without a leading space inside the comment.
- **Don't strip attributes silently.** If you set an attribute on a block, the inner HTML must reflect it. If you don't need the attribute, omit it from the JSON entirely rather than setting it and forgetting to render its effect.

After generating, offer to save the markup to a file (e.g. `block-markup/<descriptive-name>.html` in the repo). Pasting from terminal can introduce smart quotes, line wrapping, or trailing whitespace that mangle the JSON inside attribute strings — copying from a saved file is more reliable.

## Workflow

1. **Read what the user asked for.** They may describe an aesthetic ("minimal portfolio", "bold magazine", "kids brand"), list specific links, or leave it open. If the request is genuinely vague, ask one targeted clarifying question — don't bombard.
2. **Pick a structure that fits.** Three common shapes (you're not limited to these — adapt):
   - **Sectioned**: small section labels + multiple vertical `core/navigation` blocks + optional CTA at the bottom. Good for retail or SaaS sites with many links.
   - **Editorial**: a hero block (image + headline) followed by a single nav and supporting content (images, quotes, secondary nav). Good for magazines and portfolios with personality.
   - **Restrained**: just the close button + a single large nav, vertically centred, maybe a quiet tagline. Good for minimal portfolios or single-product sites.
3. **Choose blocks deliberately.** Beyond `core/group` / `core/navigation` / `core/navigation-overlay-close`, useful blocks include `core/heading`, `core/paragraph`, `core/buttons`, `core/image`, `core/spacer`, `core/separator`, `core/social-links`, and (carefully) `core/cover`. Use `https://picsum.photos/id/<n>/<w>/<h>` for placeholder images.
4. **Generate the markup.** Output it directly — no surrounding prose, no code fence (the user will copy raw).
5. **Offer to save it.** After producing the markup, offer to save it to a file in the repo's `block-markup/` directory so the user can copy from there rather than terminal.

## Block syntax reference

### Outer wrapper

```
<!-- wp:group -->
<div class="wp-block-group">...children...</div>
<!-- /wp:group -->
```

### Close button row (top-right, top level)

```
<!-- wp:group {"style":{"spacing":{"blockGap":"0"}},"layout":{"type":"flex","justifyContent":"right","flexWrap":"nowrap"}} -->
<div class="wp-block-group"><!-- wp:navigation-overlay-close {"style":{"spacing":{"padding":{"top":"var:preset|spacing|40","right":"var:preset|spacing|40","bottom":"var:preset|spacing|40","left":"var:preset|spacing|40"}}},"fontSize":"large"} /--></div>
<!-- /wp:group -->
```

### Vertical navigation

```
<!-- wp:navigation {"showSubmenuIcon":false,"submenuVisibility":"always","overlayMenu":"never","fontSize":"large","layout":{"type":"flex","orientation":"vertical","justifyContent":"left"}} -->
<!-- wp:navigation-link {"label":"Home","url":"#"} /-->

<!-- wp:navigation-link {"label":"About","url":"#"} /-->
<!-- /wp:navigation -->
```

Note that `core/navigation` source markup lists `navigation-link` children directly — no wrapper div in the saved markup.

### Section with heading + nav

```
<!-- wp:group {"style":{"spacing":{"blockGap":"var:preset|spacing|30"}},"layout":{"type":"flex","orientation":"vertical","flexWrap":"nowrap","justifyContent":"stretch"}} -->
<div class="wp-block-group">
  <!-- wp:heading {"level":3,"fontSize":"small"} -->
  <h3 class="wp-block-heading has-small-font-size">Shop</h3>
  <!-- /wp:heading -->
  <!-- wp:navigation {...vertical nav as above...} -->
  ...
  <!-- /wp:navigation -->
</div>
<!-- /wp:group -->
```

### Stretch CTA button

```
<!-- wp:buttons {"layout":{"type":"flex","justifyContent":"stretch","flexWrap":"nowrap"}} -->
<div class="wp-block-buttons"><!-- wp:button {"width":100,"fontSize":"medium"} -->
<div class="wp-block-button has-custom-width wp-block-button__width-100"><a class="wp-block-button__link has-medium-font-size has-custom-font-size wp-element-button">Subscribe</a></div>
<!-- /wp:button --></div>
<!-- /wp:buttons -->
```

### Image (preferred over cover for most hero use)

```
<!-- wp:image {"sizeSlug":"large","style":{"border":{"radius":"var:preset|spacing|20"}}} -->
<figure class="wp-block-image size-large has-custom-border"><img src="https://picsum.photos/id/1080/1200/800" alt="" style="border-radius:var(--wp--preset--spacing--20)"/></figure>
<!-- /wp:image -->
```
