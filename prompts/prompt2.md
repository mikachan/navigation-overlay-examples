Generate WordPress block markup for a mobile navigation overlay (template part area: "navigation-overlay") for {{ describe the site — e.g. "a minimal black-and-white photography portfolio" or "a bold editorial food magazine" }}.

The overlay is triggered from a hamburger button and renders full-screen, primarily on mobile viewports (portrait, ~375–430px wide). Surprise me with the layout — oversized typography, full-bleed imagery, layered sections, split top/bottom panels, etc. are all fair game, as long as the result feels great on a phone.

Constraints:
- Output block markup only, with HTML comment delimiters. I will paste it into the overlay editing canvas.
- Must include core/navigation-overlay-close (with a generous touch target) and at least one core/navigation block in vertical orientation with large, tappable links.
- Design mobile-first: single column, comfortable line heights, scrollable if content exceeds the viewport, plenty of breathing room.
- Use only theme.json design tokens (var:preset|color|…, var:preset|font-size|…, var:preset|spacing|…). No hardcoded values.
- Feel free to use core/cover, core/image, core/heading, core/paragraph, core/buttons, core/spacer, core/separator, etc. Use `https://picsum.photos/id/<n>/<w>/<h>` for placeholder image URLs.
- Prefer `core/image` inside a `core/group` over `core/cover` for hero sections — cover backgrounds often fail to render after paste. If you do use `core/cover`, the `url` attribute on the cover must exactly match the `src` of the inner `<img class="wp-block-cover__image-background">`.
- Set `"overlayMenu":"never"` on `core/navigation` so it doesn't try to nest another overlay.
- Keep the outermost `core/group` wrapper minimal — no `minHeight`, no flex `layout`, no `backgroundColor`/`textColor`. Put colours on inner sections or the template part; padding on the outer wrapper is fine.
- Place `core/navigation-overlay-close` as a direct child of the outer wrapper (inside its own right-aligned flex group) — not nested inside a `core/cover` or other block with `contentPosition`.

Return the markup only — no explanatory prose.