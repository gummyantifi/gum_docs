---
icon: book
---

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
- Each book has its own content. The item's metadata holds the book's ID, and the pages are stored in the database under that ID.
- Players can jump directly to a specific page.

### Text formatting

Players can set, per text selection:

- Font color (four colors).
- Font family (four fonts).
- Font size.
- Bold, italic, and underline.
- Alignment: left, center, right.

### Images

- Players insert images by pasting a direct link that ends in `.png`, `.jpg`, `.jpeg`, `.webp`, `.gif`, or `.svg`.
- Images can be positioned anywhere on the page and resized.
- Images can be combined with formatted text.

### Tearable pages

- A player can tear a page out of a book and use it as a physical item.
- The page can be given to another player.
- The page can be placed or hung somewhere in the world, and removed later.
- You configure whether a torn page is cleared from the original book or stays in it.

Typical uses are notes on a door, a menu on a bar, posters, announcements, contracts, letters, maps, and diaries.

## Requirements

- **gumApi**. Install it first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).
- **oxmysql**.

## Install

1. Put the `gum_book` folder in your resources and start it after `gumApi` and `oxmysql`.
2. Add two items to your inventory items. The names must match `Config.BookId` and `Config.PageId`:
   - `Notes`: the book.
   - `Notes_Paper`: a torn page.
3. You do not need to import any SQL. On start the script creates its tables if they do not exist. With the default `Config.SQLTables = "books"` they are:

| Table | Holds |
| --- | --- |
| `books` | One row per book, with its owner. |
| `books_pages` | The text and images of each page, one row per page. |
| `books_paper` | The content of each torn page. |
| `books_drop` | Pages placed in the world, with their position. |

A book gets its ID the first time a player uses the item. A book item with no ID yet is a blank book.

## Configuration

Everything is in `config.lua`.

| Key | Default | What it does |
| --- | --- | --- |
| `Config.BookId` | `"Notes"` | Item name of the book. |
| `Config.PageId` | `"Notes_Paper"` | Item name of a torn page. |
| `Config.MaxPages` | `50` | Number of pages a new book gets. |
| `Config.ClearPageAfterTear` | `true` | `true` empties the page in the book after it is torn out. `false` leaves the text in the book. |
| `Config.PlayerNoteCanRemoveOnlyOwner` | `true` | `true` lets only the player who placed a page pick it up. `false` lets anyone pick it up. |
| `Config.SQLTables` | `"books"` | Base name of the SQL tables. The other three tables use it as a prefix. |
| `Config.Debug` | `true` | Prints debug lines. Set it to `false` on a live server. |

`Config.Language` holds the prompt labels (place, pick up, read, rotate) and the editor labels (text decoration, align, font style, color, tools, page navigation).

The font list, the font size limits, and the four text colors are not in `config.lua`. They are set in the `html` folder (`index.js` and `index.html`).

If you change `Config.SQLTables` on a server that already has books, rename the existing tables too. Otherwise the script creates new empty tables and the old books stop opening.

## Customers also ran into these

### Other players cannot pick up a torn page that was placed in the world

Set `Config.PlayerNoteCanRemoveOnlyOwner = false`. Other players can then pick up placed pages.

### The page navigation buttons have no text after updating to 1.0.8

Version 1.0.8 added page navigation and five new lines in `Config.Language`. If you kept your old `config.lua`, add them:

```lua
    bookPageLabel = "Page",
    bookPageGo = "Go",
    bookPagePrevious = "Previous page",
    bookPageNext = "Next page",
    bookPageJump = "Jump to page",
```

### Content is lost when saving a large book

Since version 1.0.8 the script saves each page on its own, in the `books_pages` table, so a large book is no longer written in one piece. Update to 1.0.8 or later and add the new `config.lua` lines above.

### The book layout is wrong on some screen resolutions

The resolution calculation was fixed on 2026-08-22 (css and js). Update the `html` folder.

### The tear page button has no room on lower resolution screens

Fixed on 2026-08-27 (css). Update the `html` folder.
