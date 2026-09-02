# Ambassadors Club Website v13

This version fixes the JavaScript initialization bug that prevented navigation and other buttons from working. The page-tab target map is now initialized before the first page render, avoiding a JavaScript temporal-dead-zone error.

## Logos
The previous AI-like hand-drawn logo artwork has been removed. The header/identity now points to the official General Conference Youth Ministries Ambassadors logo source, and the footer points to an actual Adventist Youth emblem image rather than a generated SVG.

Important: the exact official image files could not be fetched as raw bytes in this environment, so these two images are not falsely described as locally embedded. If you want the site to be completely self-contained with the exact official files, upload the official Ambassador logo and AY emblem image files and they can be embedded into the HTML/package.
