Generate WordPress block markup for a mobile navigation overlay template part (area: "navigation-overlay"). The overlay is triggered from a hamburger button and renders full-screen, primarily on mobile
  viewports (portrait, ~375–430px wide). The aesthetic is restrained and quiet — think editorial whitespace, not marketing density.

  Requirements:
  - Output only block markup with HTML comment delimiters (e.g. <!-- wp:group --> … <!-- /wp:group -->). I will paste it directly into the overlay editing canvas.
  - Include the core/navigation-overlay-close block, top-right, small and unobtrusive but with a generous touch target.
  - One core/navigation block, vertical orientation, holding the primary links only (no sub-sections, no section headings). Link type-size should be large and confident — closer to a display size than a body size.
  - Vertically centred within the viewport with substantial padding above and below; left-aligned links sitting against a comfortable left margin.
  - Optionally one quiet supporting element at the bottom — e.g. a small core/paragraph with a tagline, or a tight core/social-links row. Nothing more.
  - Use only theme.json design tokens (var:preset|color|…, var:preset|font-size|…, var:preset|spacing|…). No hardcoded values.
  - Background should be the site's base/canvas colour; text should be the primary contrast colour. No accent backgrounds, no borders, no decorative blocks.

  Return the markup only — no prose, no template-part wrapper.