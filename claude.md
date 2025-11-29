# Gift Dashboard Project

A single-page Christmas gift tracking app for personal/family use.

## Tech Stack

- **Single HTML file** — Everything (HTML, CSS, JS) lives in `index.html`
- **React 18** — Loaded via CDN, uses Babel for in-browser JSX transformation
- **Tailwind CSS** — Loaded via CDN (cdn.tailwindcss.com)
- **JSONBin.io** — Free JSON storage API for cloud persistence

## Architecture

This is intentionally simple "vibe code" — no build step, no node_modules, just open the HTML file in a browser.

Data persistence works in two layers:
1. **localStorage** — Immediate cache for fast loading and offline use
2. **JSONBin** — Cloud sync for cross-device access (debounced 1 second after changes)

## Data Structure

Each gift object:
```json
{
  "id": "1234567890",
  "recipient": "Person Name",
  "item": "Gift description",
  "price": 0,
  "budget": 100,
  "status": "Idea|Ordered|Received|Wrapped",
  "link": "https://...",
  "notes": "Free text",
  "assignedTo": "Tom|Evelyn|",
  "orderDate": "2025-12-01",
  "expectedDelivery": "2025-12-15"
}
```

## JSONBin Configuration

The bin ID and API key are at the top of the `<script type="text/babel">` section:
```javascript
const JSONBIN_BIN_ID = '692a226cae596e708f775477';
const JSONBIN_API_KEY = '$2a$10$Me1AY5oZIu8mi4yVqzTIx.O/LuY2a3CzbwYyf.IfwTIoPc4DYYex6';
```

## Key Patterns

**Adding new fields:**
1. Add to the `addGift()` template object
2. Add input field in the edit form (inside the `editingId === gift.id` conditional)
3. Add display element in the read-only view
4. Update `generateMarkdown()` if it should appear in exports
5. Existing data gracefully handles missing fields (use `gift.newField || ''`)

**Status cycle:** Idea → Ordered → Received → Wrapped (click badge to cycle)

**Sync indicator:** Shows in header — Synced (green) / Saving... (blue) / Offline (amber) / Error (red)

## Conventions

- Keep everything in one file — this is a feature, not a limitation
- Use Tailwind utilities, no custom CSS classes except for animations
- Icons are inline SVG components (see `Icons` object)
- All user-facing text is in the component JSX, not extracted to constants
- Dates stored as ISO strings (YYYY-MM-DD) from native date inputs

## Common Tasks

**Test changes locally:** Just open `index.html` in a browser. Use browser DevTools console for debugging.

**Reset to cloud data:** Click the sync status indicator in the header, or clear localStorage in DevTools.

**Check JSONBin directly:** https://api.jsonbin.io/v3/b/692a226cae596e708f775477/latest (needs auth header)

## Gotchas

- Babel transformation happens at runtime — syntax errors won't show until you open the page
- No hot reload; refresh the browser after changes
- JSONBin free tier: ~10,000 requests/month, which is plenty for personal use
- If two people edit simultaneously, last-save-wins (no conflict resolution)