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
- Font size, bold, italic, underline, color, and alignment can be changed.
- Lines, frames, and other visual elements can be inserted.
- Content can be arranged freely on each page.

### Images and design

- Images are added by pasting a direct link that ends in `.png`, `.jpg`, `.jpeg`, `.webp`, `.gif`, or `.svg`.
- Images can be resized and positioned.
- Decorative lines and frames can be added to build a layout.

### Multi-page newspapers

- A newspaper can have multiple pages, with automatic page handling.
- Pages are shown as a left and right spread in a flipbook interface with page turning.
- The complete newspaper can be previewed before publishing.

### Publishing and items

- A player with the right job uses an empty newspaper item to open the editor.
- When the editor is closed with content in it, the script asks how many copies to print. The player needs that many empty newspaper items.
- Each printed copy is the same finished newspaper item. The item's metadata holds the newspaper's ID, and the content is stored in the database under that ID. You do not need a separate item for each newspaper.
- Any player can use a finished newspaper item to read it.
- Suggested uses: newspapers, news agencies, player journalism, government announcements, businesses, events, advertisements, and community stories.

## Requirements

- **gumApi**. Install it first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).
- **oxmysql**.

## Install

1. Put the `gum_newspapers` folder in your resources and start it after `gumApi` and `oxmysql`.
2. Add two items to your inventory items. The names must match `emptyId` and `fullId` in `Config.newsPapers`:
   - `Empty_Newspapers`: the empty newspaper that opens the editor.
   - `Newspapers`: the finished newspaper.
3. You do not need to import any SQL. On start the script creates the `newspapers` table if it does not exist. It has two columns: `id` and `text` (the newspaper content).

## Configuration

Everything is in `config.lua`.

### Publishers (`Config.newsPapers`)

Each entry is one publisher. The key is the publisher's name.

```lua
Config.newsPapers = {
    ["Valentine News"] = {
        emptyId = "Empty_Newspapers",
        fullId = "Newspapers",
        jobs = {"UG", "B_News", "SD_News"},
        removeEmptyId = true,
    },
}
```

- `emptyId`: the item that opens the editor.
- `fullId`: the item given to the player when the newspaper is printed.
- `jobs`: the jobs that can open the editor with the empty item. Replace the example job names with your own.
- `removeEmptyId`: `true` takes one empty item for each printed copy.

To give a second job group its own newspaper, add another entry with its own item names.

### Lines and frames (`Config.Presets`)

`Config.Presets.lines` and `Config.Presets.frames` list the images offered in the editor sidebar. The defaults point to three line images and three border images in `html/presets/`. To add your own, put a `.png` in that folder and add its path to the list.

### Language (`Config.Language`)

`Config.Language.prompts.howManyNewspapersYouWantCreate` holds the button label and the question shown when a player prints copies.

## Customers also ran into these

### The server console shows a "d" argument error

This was a server-side bug. It was fixed on 2026-09-05. Update `server.lua`.
