---
name: share-and-export
description: Share a ShowMeOnMap map as a link, export it as PNG/JPEG/PDF, or export a layer's data. Use when the user asks for a link, an image, a download, or the underlying data of a map.
---

# Share and export

All exports are free.

| User wants | Tool | Notes |
|---|---|---|
| A link | none — use `shareable_url` from the last envelope | Opens the live, interactive map with sources |
| An image / PDF | `export_image` (`format`: `png` · `jpeg` · `pdf`; `legend` default true) | Returns a link that renders and downloads the file **in the user's browser** — there is no server-side image. Give the link; don't claim a file was attached. |
| The data | `export_layer_data` with `layer_id` | Data keeps its source licence; mention the attribution when handing it over |
| The full map definition | `export_workspace` | For re-importing or debugging, not for end users |

Before exporting:

- Make sure the map shows what the user last asked for — call `get_state` if
  several changes were made.
- Exports capture the **current** filters. The legend and layer subtitle may
  not mention a filter you applied, so tell the user what the image shows
  ("filtered to depth > 50 km").
- If a filter left a layer empty, say so and offer to clear it before
  exporting, rather than handing over a map with nothing on it.

For a map that will be read by others, consider running the key analysis with
`story: true` (skill `run-spatial-analysis`) so the link opens as a guided
read.
