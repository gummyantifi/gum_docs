# GUM Book

GUM Book is a book and journal system. Players buy book items, write and format their own pages, add images, and tear pages out to hand over or place in the world.

## At a glance

- Video: [GUM Book](https://www.youtube.com/watch?v=eKlKKLtc95s)
- Store: [GUM Scripts on Tebex](https://gum-scripts.tebex.io/category/redm)

## What it does

### Books and journals

- Players buy book items from shops and create their own journals.
- A player can open a book and start writing right away.
- You choose how many pages a book contains.
- Each book has its own content, stored using item metadata.
- Players can jump directly to a specific page.

### Text formatting

Players can set, per text element:

- Font color.
- Font family.
- Font size.
- Bold, italic, underline, and other text styles.

### Images

- Players insert images using direct links (`.png`, `.jpg`, `.jpeg`, and other supported formats).
- Images can be positioned anywhere on the page and resized.
- Images can be combined with formatted text.

### Tearable pages

- A player can tear a page out of a book and use it as a physical item.
- The page can be given to another player.
- The page can be placed or hung somewhere in the world, and removed later.
- You configure whether a torn page is removed from the original book or stays in it.

Typical uses are notes on a door, a menu on a bar, posters, announcements, contracts, letters, maps, and diaries.

### What you can configure

- Book items and page limits.
- Text formatting, fonts, font sizes, and text colors.
- Image support.
- Page removal behavior and page placement.
- Metadata handling.

## Before you install

Install **gumApi** first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).

## Configuration notes

- `Config.SQLTables`: sets a custom SQL table name. Added 2026-08-23.

```lua
Config.SQLTables = "books"
```

- `Config.PlayerNoteCanRemoveOnlyOwner`: set to `false` so that other players can pick up placed notes.

```lua
Config.PlayerNoteCanRemoveOnlyOwner = false
```

- Page navigation labels: version 1.0.8 (2026-10-30) added these lines to `config.lua`. Add them to your own `config.lua` when you update.

```lua
    bookPageLabel = "Page",
    bookPageGo = "Go",
    bookPagePrevious = "Previous page",
    bookPageNext = "Next page",
    bookPageJump = "Jump to page",
```

## Common problems and fixes

### Other players cannot pick up a torn page that was placed in the world

Set `Config.PlayerNoteCanRemoveOnlyOwner = false`. Other players can then pick up placed notes.

### Content is lost when saving a large book

Version 1.0.8 (2026-10-30) changed book saving so pages are saved individually, which reduces the risk of losing content in large books. Update to 1.0.8 and add the new `config.lua` lines.

### The book layout is wrong on some screen resolutions

The resolution calculation for different resolutions was fixed on 2026-08-22 (css and js).

### The tear page button has no room on lower resolution screens

Fixed on 2026-08-27 (css).

## Recent changes

- 2026-10-30: Version 1.0.8. Added page navigation so players can jump to a specific page. Books now save pages individually. Five new lines in `config.lua`. Replace `client.lua`, `html/index.css`, `html/index.js`, `html/index.html`, `html/indexBp.css`, `html/indexBp.js`, and `server.lua`.
- 2026-08-27: Fixed the space for the tear page button on lower resolution screens (css).
- 2026-08-23: Added `Config.SQLTables` for a custom SQL table name (server).
- 2026-08-22: Fixed the resolution calculation on different resolutions (css and js).
- 2026-08-21: Fixed escrowed config files.

*This page was built from the script's announcement, its changelog, and resolved support tickets. For anything not covered here, open a ticket in the GUM Scripts Discord.*
