---
icon: utensils
---

# GUM Cooking

GUM Cooking adds four food and drink activities to your server: cooking in a cauldron, cutting meat, distilling, and grinding with a mortar and pestle. Each one uses recipes you define.

## At a glance

- Video: [GUM Cooking](https://www.youtube.com/watch?v=KB5JsZwwEc8)
- Frameworks: RSG compatibility was added on 2026-05-31.

## What it does

### Cooking in a cauldron

- You can create custom recipes and lock them to a specific job or make them available to everyone.
- You can set both the cooking time and the time before food burns.
- Burnt food can be configured as a separate item and used for other recipes or purposes.
- Cooking has quality levels, from low quality to high quality.

### Meat cutting

- You can configure how much meat is processed and how many items the player receives as a reward.
- You can set how many cuts are required to complete the minigame.

### Distilling

- You can create custom recipes and set custom distillation times.
- Recipes can be restricted to specific jobs.
- If the wrong ingredients or recipe are used, the player can receive a different product, such as adulterated alcohol.

### Mortar and pestle

- Used for grinding powders, berries, and other ingredients.
- You can create custom recipes.
- Each recipe can be locked to a specific required item.
- Players can select the number of pieces to produce.

## Before you install

Install **gumApi** first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).

## Configuration notes

- `pressMatch`: a translation line added on 2025-02-06.

```lua
pressMatch = "Press match for start cook."
```

- `Config.Lang`: holds the language texts. The "Item Count" text is loaded from here (fixed 2025-02-06).
- The 2025-02-04 update changed the language pack and added the option to set the number of pieces for cutting meat. Recipes and other content stayed the same. Compare your files with the new ones when you update.
- UI files: the developer has said that, as far as he knows, the UI files (HTML, CSS, JavaScript) are not protected by escrow and can be modified to your liking.

## Common problems and fixes

### After a server restart the script does not work until it is restarted again

Cause: items were not loaded from the server after a server restart.

Fix: update the server file to the 2026-07-02 version or later.

### An error appears when a player does not select an amount in the mortar, cooking, or distilling

Cause: the value came back as nil when nothing was selected.

Fix: update `index.js` to the 2025-11-12 version or later. An empty value is now replaced with "1".

### The cookbook does not display correctly

Fixed on 2025-02-06.

### The mortar inventory does not open

Cause: a problem loading the language for the mortar.

Fix: fixed on 2025-02-06.

### Opening the mortar and then another minigame closes the cauldron

Fixed on 2025-02-04.

### "Item Count" is not translated in cooking or the mortar

Fixed on 2025-02-06. The text now loads from `Config.Lang`.

## Recent changes

- 2026-07-02: Fixed items not loading from the server after a server restart (server).
- 2026-05-31: Visual and functional update. The script is now compatible with RSG.
- 2025-11-12: An unselected value in the mortar, cooking, or distilling is now replaced with "1" (`index.js`).
- 2025-07-06: Added a function to increase values when using the mortar. Fixed minor language pack issues (client and `index.js`).
- 2025-05-13: Fixed language conversion issues in `Cooking.js`.
- 2025-02-06: Fixed item counting on the cutting board, the cookbook display, mortar language loading, and the "Item Count" translation. Added the `pressMatch` translation line.
- 2025-02-04: Redesigned cauldron cooking interface. Improved cutting board appearance, adjusted minigame, added missing notifications, and added a setting for the number of pieces when cutting meat. The mortar got an option to select the number of pieces to produce. The language pack was updated, so compare your files.

*This page was built from the script's announcement, its changelog, and resolved support tickets. For anything not covered here, open a ticket in the GUM Scripts Discord.*
