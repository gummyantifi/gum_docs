# GUM Crafting: troubleshooting

This page lists the `gum_craft` problems that support tickets and the changelog show a fix for, plus what changed in recent updates.

## Check these first

1. **gumApi is up to date.** The rebuilt `gum_craft` (2026-07-16) needs the new gumApi. An old gumApi produces nil value errors.
2. **You can see the gumApi download.** It is only visible with the customer role in the GUM Scripts Discord.

## Problems and fixes

### Installation and gumApi

#### "attempt to call a nil value (field 'getAllItems')"

Example from a ticket:

```
[script:gum_craft] SCRIPT ERROR: attempt to call a nil value (field 'getAllItems')
```

Cause: `getAllItems` comes from gumApi, and the installed gumApi is too old. The 2026-07-16 release of `gum_craft` needs the new gumApi (the changelog names the `serverData` update).

Fix: update gumApi, then start `gum_craft` again.

#### I cannot find the gumApi download

The gumApi download is in a channel that only shows once you have the customer role in the GUM Scripts Discord. You get the role after purchase. If you bought the script and do not have the role, open a ticket and give the Tebex transaction ID (TBX ID) from your purchase email.

#### Does it work with the older VORP inventory?

Answer from 2026-08-25: `gum_craft` is built for the latest VORP inventory update. The developer expects it to work with the older version too. If it does not, open `serverDataVORP` and adjust the structure to the older version by hand.

### Recipes

#### Players can craft a recipe anywhere even though I set a prop

A prop on a recipe means the player has to be near that prop to craft. It only works when `["propHash"]` uses the full structure. With only a bare model name in the table the entry is missing arguments, and the player can still craft anywhere.

```lua
["propHash"] = {
    {
        ["name"] = "Pracovní Stůl",
        ["hash"] = "p_benchwork01x",
        ["id"] = "Furniture_117"
    }
},
```

This is the structure for the current `gum_craft`. The old and new versions use different config structures. See [Manual Crafting Configuration](configWithoutHelper.md) for a full recipe.

#### My old recipes do not load in the new version

The old and new `gum_craft` use different config structures. Convert the old config with CraftingHelper. The steps are in [Converting Old Configuration](configOldConverting.md). Back up your old files before you update.

#### The amount from the config is ignored, or the item title always shows 1x

Two fixes to the js files cover this. The 2026-07-27 update fixed the item count from the configuration file not being applied to the recipe. The 2026-08-01 update fixed the item title always showing `1x` when a different amount (for example `5x`) was configured. Update to the 2026-08-01 release or later.

#### Crafting an item with metadata fails

Fixed in the 2026-08-16 server update.

### Players

#### A player's favorite recipes disappeared

Favorite recipes are stored in the client cache. A player who clears their cache loses their favorites and has to set them again. Favorites are also tied to the specific crafting point or item they were set on, so they do not carry over to another one.

### CraftingHelper

#### Is it safe to run CraftingHelper on a live server?

No. Use CraftingHelper only where there are no players present. The changelog warns that it is not protected against admin permissions or similar situations.

## Settings added in updates

Newest first.

- **2026-08-16.** Server: fixed crafting items with metadata.
- **2026-08-01.** js: fixed the amount in the item title always showing `1x`.
- **2026-07-27.** js: fixed the item count from the configuration file not being applied to the recipe.
- **2026-07-16.** `gum_craft` rebuilt from the ground up. What the owner has to do:
  - Update gumApi first. This release needs the new gumApi (`serverData` update).
  - Back up your old files.
  - Convert existing recipes to the new format with CraftingHelper, or write them by hand in the new structure.
  - At release the build was published in the portal under `gum_craft`, three dots menu, Beta/Candidate version.

  New in this release: inventory-based crafting prediction and queue, color indicators per recipe (green: can craft, red: missing materials, orange: recipe can be discovered, gray: blueprint only), recipe pinning, favorites stored in the client cache, canceling queue entries by clicking them, automatic material calculation for larger amounts, and CraftingHelper. Opening by command, item, and coordinates is unchanged.
- **2025-03-07.** Client: fixed an error message when canceling the input. (Version before the 2026-07-16 rebuild.)
- **2025-02-08.** Client, server, js: reworked information transfer between client and server. Translations added (version before the 2026-07-16 rebuild):
  ```lua
  [13] = "Need profession: "  
  [14] = "L: "  
  [15] = "XP: "  
  [16] = "Find your item now"
  ```

*Built from the changelog and resolved support tickets. For anything not covered here, open a ticket in the GUM Scripts Discord.*
