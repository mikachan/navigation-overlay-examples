Generate WordPress block markup for a mobile navigation overlay (template part area: "navigation-overlay") for {{ describe the site — e.g. "a minimal black-and-white photography portfolio" or "a bold editorial food magazine" }}.

The overlay is triggered from a hamburger button and renders full-screen, primarily on mobile viewports (portrait, ~375–430px wide). Surprise me with the layout — oversized typography, full-bleed imagery, layered sections, split top/bottom panels, etc. are all fair game, as long as the result feels great on a phone.

Constraints:
- Output block markup only, with HTML comment delimiters. I will paste it into the overlay editing canvas.
- Must include core/navigation-overlay-close (with a generous touch target) and at least one core/navigation block in vertical orientation with large, tappable links.
- Design mobile-first: single column, comfortable line heights, scrollable if content exceeds the viewport, plenty of breathing room.
- Use only theme.json design tokens (var:preset|color|…, var:preset|font-size|…, var:preset|spacing|…). No hardcoded values.
- Feel free to use core/cover, core/image, core/heading, core/paragraph, core/buttons, core/spacer, core/separator, etc. Use placeholder image URLs where needed.

Return the markup only — no explanatory prose.