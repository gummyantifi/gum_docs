---
icon: hammer
---

# Advanced Crafting System

A fully configurable crafting system designed for immersive roleplay servers.

Create complex crafting stations, professions, hidden recipes, item requirements, and advanced production chains with complete control over your economy.

---

## Dynamic Crafting System

Create unlimited crafting books and recipes with an easy-to-use configuration system.

Each crafting location can have its own:
- Recipes
- Required items
- Jobs
- Professions
- Access restrictions
- Crafting rules

Whether you need a simple crafting table or a complex production system, everything can be customized.

---

## Multiple Crafting Locations

A single crafting book can contain multiple crafting locations.

Example:
- Different factories
- Workshops
- Laboratories
- Blacksmith locations
- Hidden production areas

Players can access the same crafting system from multiple places.

---

## Profession Based Crafting

Connect crafting with professions and progression.

Recipes can require:
- Specific profession
- Profession level
- Grade requirements

Players gain profession experience by crafting, creating a natural progression system.

---

## Discovery System

Create hidden recipes that players must discover themselves.

With the discovery system, players can:
- Find blueprints
- Experiment with different materials
- Discover hidden crafting combinations

Players can try unknown recipes by placing different items into the discovery system and finding the correct combination.

Perfect for:
- Secret recipes
- Rare production
- Underground markets
- Exploration gameplay

---

## Advanced Recipe System

Each recipe supports:

- Required materials
- Crafting time
- Output amount
- Profession experience
- Metadata
- Props
- Returned items
- Hidden recipes
- Custom descriptions

Create simple items or complex production chains.

---

## Metadata Support

Crafted items can contain custom metadata.

Examples:
- Quality
- Durability
- Custom values
- Item information

This allows unique crafted items with different properties.

---

## Return Materials System

Recipes can return items after crafting.

Useful for:
- Empty containers
- Tools
- Reusable materials
- Production equipment

---

## Crafting Props

Add physical objects required during crafting.

Examples:
- Tables
- Machines
- Workstations
- Production equipment

Make crafting feel more interactive and immersive.

---

## Access Control

Control who can use each crafting system.

Restrictions can be based on:
- Items
- Jobs
- Professions
- Grades

Create public workshops or restricted production areas.

---

## Fully Configurable

Almost every part of the crafting system can be customized.

Configure:
- Crafting locations
- Recipes
- Requirements
- Professions
- Discovery mechanics
- Metadata
- Production systems

Build anything from a simple crafting table to a complete economy-based production system.

---

## Requirements

- `gumApi`, started before `gum_craft`. The script takes players, jobs, inventory, usable items, notifications, prompts and the item image folder from it.
- `oxmysql`. The script calls it for its two database tables.

The resource folder is `gum_craft`. CraftingHelper (folder `craftingHelper`) is a separate, optional resource for building recipes in game. It also needs `gumApi`.

## Installation

1. Put `gum_craft` in your resources folder and start it after `gumApi` and `oxmysql`.
2. Do not import any SQL. On start the script creates the tables `craft` (profession experience per character) and `craft_data` (discovered recipes per character) if they do not exist.
3. Make sure every item named in your recipes exists in your inventory: book items, ingredients, crafted items, returned items and blueprint items.
4. Edit `config.lua` and `configRecipes.lua`, then restart `gum_craft`.

## Configuration files

### `config.lua`

- `Config.CraftingCommand`: the chat command that opens the crafting menu. Default is `"vyroba"`.
- `Config.Language`: all texts, in three groups: `notifications`, `prompts` and `menu`. The shipped texts are Czech. `prompts.promptCraftButton` holds the prompt label and the key hash used at crafting coordinates.
- `Config.Profession`: one entry per profession. Each entry lists the experience needed for level 1, 2, 3 and so on.
- `Config.ProfessionDesc`: the description shown for each profession in the menu. It accepts HTML. Use the same profession names as in `Config.Profession`.

A recipe that requires a profession must use a name that exists in `Config.Profession`. With an unknown name the player's level counts as 0.

### `configRecipes.lua`

This file holds one table, `Config.Items`, with all crafting books and their recipes. You can write it by hand or generate it:

- [Config with the helper](configHelper.md)
- [Config without the helper](configWithoutHelper.md)
- [Converting an old config](configOldConverting.md)

## How players open crafting

Each book in `Config.Items` opens in one of three ways:

- By command: books with `["command"] = true` are merged into one menu, opened with `/vyroba` (or your `Config.CraftingCommand`). The player sees the books whose `["job"]` list is empty or includes their job.
- By coordinates: books with entries in `["coords"]` show a prompt when the player stands within 1 meter of a point.
- By item: a book with `["command"] = false` and no coordinates is registered as a usable item. The item name is the book key.

## In the menu

- Recipes are colored: green when the player can craft, red when materials are missing, orange when the recipe is locked and can be self-discovered, gray when it is locked without self discovery (blueprint only).
- Crafting jobs are queued, up to 7 at a time. Clicking a queue entry cancels it.
- Players can pin up to 5 recipes and mark favorites.
- If a recipe has props, the player must stay within 2 meters of the prop. Walking away clears the queue. The same happens when the player walks more than 1 meter away from a crafting coordinate.
- Using a blueprint item teaches the recipe and removes one blueprint.

## Troubleshooting

Known problems and their fixes are on the [troubleshooting page](troubleshooting.md).
