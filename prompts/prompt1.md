Generate WordPress block markup for a mobile navigation overlay template part (area: "navigation-overlay"). The overlay is triggered from a hamburger button and renders full-screen, primarily on mobile viewports (portrait, ~375–430px wide).

Requirements:
- Output only block markup with HTML comment delimiters (e.g. <!-- wp:group --> … <!-- /wp:group -->). I will paste it directly into the overlay editing canvas.
- Include the core/navigation-overlay-close block, positioned top-right, with a generous touch target.
- Layout: a single vertically-stacked column containing 3 sectioned link groups — e.g. "Shop", "Learn", "Company" — each with:
    - A small heading (h3-style) acting as the section label.
    - A core/navigation block set to vertical orientation, with large, tappable link sizes.
- Below those, a featured section with a heading, short paragraph, and a full-width core/buttons CTA.
- Wrap everything in a core/group with vertical flex layout, generous padding, and comfortable spacing between sections so the content is scrollable and readable on a phone.
- Use only theme.json design tokens for colours, spacing, and font sizes (var:preset|color|…, var:preset|spacing|…, var:preset|font-size|…). Do not hardcode values.
- Background should use a subtle surface colour; text should use the contrast colour.
- Set `"overlayMenu":"never"` on `core/navigation` so it doesn't try to nest another overlay.
- Keep the outermost `core/group` wrapper minimal — no `minHeight`, no flex `layout`, no `backgroundColor`/`textColor`. Put colours on inner sections or the template part; padding on the outer wrapper is fine.
- Place `core/navigation-overlay-close` inside its own right-aligned flex group. Parent blocks with `contentPosition` (like `core/cover`) can pin it somewhere unexpected — if you nest it inside one, verify it still lands top-right; otherwise keep it as a direct child of the outer wrapper.
- Use consistent horizontal padding on every direct child of the outer wrapper (or put padding on the outer wrapper and let children sit naturally) — don't let images go edge-to-edge while text is indented.

Return the markup only — no prose, no template-part wrapper.