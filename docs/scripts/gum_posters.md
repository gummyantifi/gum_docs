---
icon: scroll
---

# GUM Posters

GUM Posters gives your RedM server notice boards where players pin images by link. Boards can be tied to a city, shared globally, or reserved for events.

## At a glance

- Video: [GUM Posters on YouTube](https://www.youtube.com/watch?v=Q-5ZtjN7vec)
- Frameworks: RedEM, RedEM Reboot, VORP, GUM, RSG, QBR

## What it does

### Board types

- **City board:** content is locked to that specific board.
- **Global board:** displays all boards except event boards.
- **Event board:** displays only event-related posters.

### Per-board settings

- A custom name (prompt text) and map marker for the board, with its own blip appearance, or no marking at all.
- Open the board by looking at it (targeting) or by coordinates.
- Use a board model of your choice, or turn the model off.
- Restrict the board by job: which jobs can use it, which can remove pinned posters, and which can see the names of the people who placed them.
- Block identical images from being pinned to the same board.
- A cleaner that deletes posters older than a set period. Each board has its own period, so one town can use 7 days and another 20.
- A separate Discord webhook per board.

### Base settings

- Show or hide the background, so you can style the board to your own taste. The appearance is edited with CSS.
- Turn the history of pinned images on or off. With history on, an image you placed on one board is offered again when you open another board. Once the image is removed from all boards, the history is empty.
- Turn camera targeting on or off.

### Images

- Images of any resolution and shape can be used. The script detects the image size and fits it onto the board, and clicking a poster shows it in its original proportions.
- When a player adds an image, the link is checked to confirm the image exists.

## Before you install

Install **gumApi** first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).

- Updates are delivered to the purchaser's Keymaster account. Download new files from there.

## Configuration notes

### Job permissions

The `jobAccess` list was added on 2025-03-08. It controls three things per board: who can use the board, who can remove pinned posters, and who can see the names of the players who placed a poster.

### Settings added in the 2025-03-08 rework

The changelog describes these without naming their keys. The config file is commented, so look there for the exact names:

- Background on or off, and image history on or off.
- Camera target on or off.
- Board category: Global, City, or Event.
- Prompt text, blip, and blip appearance per board.
- Coordinates or targeting to open the board.
- Board model on or off.
- Blocking identical posters.
- Poster deletion time per board.
- Discord webhook per board.

## Common problems and fixes

### Adding an image shows a yes/no choice with no field for the link

This was a bug in the client and JS files. It was fixed on 2025-03-12. Update to that version or later.

### The history background still shows with the background disabled

This was fixed on 2025-03-12. Update to that version or later.

### A poster with a very long image link does not save

The link was too long to be saved to the database. This was fixed in `server.lua` on 2025-04-13. Update to that version or later.

### The camera does not target the center of the board

The camera target on the prop center was fixed in the client on 2025-03-13. Update to that version or later.

## Recent changes

- **2026-09-10:** The image link is now checked when adding a poster, to verify the image exists. Client and JS files changed.
- **2025-10-21:** Images of different resolutions are supported. The board arranges them by size, and click-to-view shows the image in its original height and width. Client, server, CSS, and JS files changed.
- **2025-04-13:** Fixed long image links failing to save to the database. `server.lua` changed.
- **2025-03-13:** Fixed the camera target on the prop center. Client files changed.
- **2025-03-12:** Fixed the image input showing yes/no in place of the link field, and the history background showing with the background disabled. Client and JS files changed.
- **2025-03-08:** Complete rework, delivered as a complete new folder. Added board categories (Global, City, Event), per-board prompt text and blips, coordinates or targeting, the board model toggle, the `jobAccess` list, blocking of identical posters, per-board deletion time, per-board Discord webhooks, CSS styling with a background option, image history, and the camera target toggle.

*This page was built from the script's announcement, its changelog, and resolved support tickets. For anything not covered here, open a ticket in the GUM Scripts Discord.*
