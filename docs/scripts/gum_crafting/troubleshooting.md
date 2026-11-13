---
icon: wrench
---

# GUM Crafting: troubleshooting

Known `gum_craft` and CraftingHelper problems and their fixes. For installation and configuration, see the [main page](index.md).

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

Cause: `gum_craft` calls `getAllItems` from gumApi when it starts, and the installed gumApi is too old to have it. The 2026-07-16 release of `gum_craft` needs the new gumApi (the changelog names the `serverData` update).

Fix: update gumApi, then start `gum_craft` again.

#### I cannot find the gumApi download

The gumApi download is in a channel that only shows once you have the customer role in the GUM Scripts Discord. You get the role after purchase. If you bought the script and do not have the role, open a ticket and give the Tebex transaction ID (TBX ID) from your purchase email.

#### Does it work with the older VORP inventory?

Answer from 2026-08-25: `gum_craft` is built for the latest VORP inventory update. The developer expects it to work with the older version too. If it does not, open `serverDataVORP` and adjust the structure to the older version by hand.

### Recipes

#### Players can craft a recipe anywhere even though I set a prop

A prop on a recipe means the player has to be within 2 meters of that prop to craft. The check reads the `hash` field of each entry in `["propHash"]`, so every entry has to be a table in the full structure. A bare model name is not enough.

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

Update first: this was fixed in the 2026-08-16 server update. Then check the recipe. `["metadata"]` has to be a list with one `type` and `value` pair per entry:

```lua
["metadata"] = {
    { ["type"] = "durability", ["value"] = 100 }
},
```

### Players

#### A player's favorite recipes disappeared

Favorite recipes are stored in the client cache. A player who clears their cache loses their favorites and has to set them again. Favorites are saved per crafted item, so a favorite shows up in every book that contains that item.

### CraftingHelper

#### Is it safe to run CraftingHelper on a live server?

No. CraftingHelper is a setup tool. Run it on a development server with no players, and stop the resource when you are done.
