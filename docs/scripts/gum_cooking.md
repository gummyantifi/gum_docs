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
- The mortar is opened by using a mortar item.
- Players can select the number of pieces to produce.

## Before you install

Install **gumApi** first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).

## Install

1. The script has no database table of its own and no SQL file to import.
2. Add these items to your items:
   - The mortar item. Default `Mortar` (`Config.HerbCutItem`). The script registers it as a usable item. Using it opens the mortar.
   - The cookbook item. Default `Cook_Book` (`Config.Cook_Book`). A player who carries it sees the recipe book in the cauldron screen.
   - The wrong-recipe drink. Default `Fake_Bottle` (`Config.TrashDrink`).
   - Every ingredient and result item named in your recipes, including the burnt item (`Fired_Food` in the shipped recipes).
3. Start the resource after `gumApi`.
4. Set `Config.Debug_Version = false` in `cfg/config.lua` on a live server.

The cauldron, cutting board, and still are not items. Players get an "Open" prompt when they stand next to one of the world props listed in the config.

## Configuration

All config files are in the `cfg/` folder and are not escrowed. The `html/` folder is not escrowed either, so you can edit the UI files (HTML, CSS, JavaScript).

### `cfg/config.lua`

- `Config.Debug_Version`: debug mode. It loads the player's jobs on resource start and prints debug lines.
- `Config.BurnDownCounterForShake` (default `1.4`): how much the burn counter drops each time the player stirs.
- `Config.Language`: all texts, grouped by cooking, cutting board, distillery, and mortar. `cookStates` holds the four quality labels in the order burnt, low, medium, high.

### `cfg/configCooking.lua`

- `Config.Cook_Book`: the cookbook item.
- `Config.Cook_Props`: the prop models that work as a cauldron. Defaults: `p_stove04x`, `p_campfirecombined01x`, `p_campfire03x`, `p_woodburningstove01x`.
- `Config.Recepts`: the cooking recipes, keyed by a recipe ID. Each recipe has two parts:

```lua
["Dried_Meat"] = {
    ["grades"] = {
        low_grade = {"Dried_Meat", 1},    -- item, time
        medium_grade = {"Dried_Meat", 6},
        high_grade = {"Dried_Meat", 9},
        burnt_grade = {"Fired_Food", 11}
    },
    ["recipe"] = {
        showed = true,
        job = nil,
        category = "Basic food",
        name = "Dried meat",
        name1 = "Salt",        label1 = "Salt",     count1 = 2,
        name2 = "Cutted_Meat", label2 = "Cut meat", count2 = 6
    }
},
```

- `grades`: the item the player gets for each quality level, and the time for that level. You can give a different item per grade.
- `recipe.showed`: `true` lists the recipe in the cookbook.
- `recipe.job`: `nil` for everyone, or a table whose keys are the allowed job names, for example `job = { ["Barman_BW"] = "Barman" }`.
- `recipe.category`: the cookbook category. `nil` puts it under the "No Categorized" text.
- `name1` to `name10`, with matching `label` and `count`: the ingredients. A recipe can have up to ten.

### `cfg/configCutting.lua`

- `Config.Cook_Board`: the prop models that work as a cutting board. Default `p_cuttingboard01x`.
- `Config.CuttingItems`: `["item to cut"] = {"result item", result count per piece, cuts needed}`.

```lua
["Meat"] = {"Cutted_Meat", 2, 3},
```

### `cfg/configDestillery.lua`

- `Config.DistilleryProp`: the prop models that work as a still. Defaults: `mp006_p_moonshiner_still03x` and `p_still02x`.
- `Config.TrashDrink`: the item given when the ingredients match no recipe.
- `Config.DestilleryRecipe`: keyed by the result item. Each recipe has `time` (seconds), `job` (same format as cooking), and `recipe`, a list of `{"item", quantity}` rows.

### `cfg/configHerbCut.lua`

- `Config.HerbCutItem`: the usable item that opens the mortar.
- `Config.Recipe`: the mortar recipes, keyed by the result item. `rewardCount` is how many result items one craft gives. The other rows are `{"item", quantity}` ingredients.

```lua
Opium_Powder = {
    rewardCount = 10,
    {"Opium", 1}
},
```

## Customers also ran into these

### After a server restart the script does not work until it is restarted again

Items were not loaded from the server after a server restart. Update the server file to the 2026-07-02 version or later.

### An error appears when a player does not select an amount in the mortar, cooking, or distilling

The value came back empty when nothing was selected. Update `html/index.js` to the 2025-11-12 version or later. An empty value is now replaced with 1.

### "Item Count" is not translated in cooking or the mortar

The texts load from `Config.Language` in `cfg/config.lua`. Translate `cookingItemCount`, `cookingItemPsc`, `mortarItemCount`, and `mortarItemPsc`.

### Texts are missing after an update

If you keep your own `cfg/config.lua`, compare its `Config.Language` with the shipped file after each update. `pressMatch` is one of the lines added by a past update.
