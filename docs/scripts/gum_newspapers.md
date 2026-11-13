---
icon: newspaper
---

# GUM Newspapers

GUM Newspapers lets players write, design, and publish newspapers in-game on RedM. A finished newspaper becomes an inventory item that can be handed out on your server.

## At a glance

- Video: [GUM Newspapers on YouTube](https://www.youtube.com/watch?v=9WKhVgy8WY0)
- Store: [GUM Scripts RedM category on Tebex](https://gum-scripts.tebex.io/category/redm)

## What it does

### Newspaper editor

- Players write and format articles in an in-game editor.
- Font size, style, and alignment can be changed.
- Lines, frames, and other visual elements can be inserted.
- Content can be arranged freely on each page.

### Images and design

- Images are added by URL, in multiple common image formats.
- Images can be resized and positioned.
- Decorative lines and frames can be added to build a layout.

### Multi-page newspapers

- A newspaper can have multiple pages, with automatic page handling.
- Pages are shown as a left and right spread in a flipbook interface with page turning.
- The complete newspaper can be previewed before publishing.

### Publishing and items

- A finished newspaper is saved as an item and can be distributed to players.
- Empty Newspaper items are printed and turned into finished newspapers.
- Newspaper data is stored in item metadata, so every newspaper uses the same base item. You do not need a separate item for each newspaper.
- Suggested uses: newspapers, news agencies, player journalism, government announcements, businesses, events, advertisements, and community stories.

### Templates and server styling

- You can customize backgrounds, fonts, text formatting, images, frames, decorative elements, and page layouts.
- Custom frames, lines, and other elements can be configured for your server.
- You can configure the available tools, newspaper settings, formatting options, and item behavior.

## Before you install

Install **gumApi** first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).

- Set two items: the empty newspaper item and the regular newspaper item. Since the 2026-08-17 rework, individual newspapers are not defined in the configuration file. When a player creates a newspaper, the script stores everything in the item's metadata and creates the newspaper item.

## Common problems and fixes

### The server console shows a "d" argument error

This was a server-side bug. It was fixed on 2026-09-05. Update the server files.

## Recent changes

- **2026-10-21:** Improved the UI functions in the editor (font size, bold, and so on). The `html` folder changed.
- **2026-09-05:** Fixed the "d" argument error in the server console. Server files changed.
- **2026-08-17:** Complete rework of the script. Newspapers are now created in the in-game editor and stored in item metadata, so the script no longer depends on the configuration file for newspaper content. You set the empty newspaper item and the regular newspaper item. Added image insertion and configurable frames, lines, and other elements.

*This page was built from the script's announcement, its changelog, and resolved support tickets. For anything not covered here, open a ticket in the GUM Scripts Discord.*
