# Manual Crafting Configuration

If you don't want to use **craftingHelper**, you can manually create crafting books and recipes directly inside your configuration.

Books go in `gum_craft/configRecipes.lua`, inside the `Config.Items` table.

This allows you to fully customize:
- Crafting locations
- Required items
- Jobs
- Professions
- Recipe discovery
- Metadata
- Props
- Returned items

Example:

```lua
["Cocaine"] = { -- Book key (unique identifier of crafting book). For a book opened by item, this is the item name the script registers as usable.
    ["item"] = "Cocaine",-- Item ID of the book. Keep it the same as the book key.
    ["job"] = {"Drugs", "Bandits"},-- Jobs allowed to access this crafting book. Empty table = everyone.
    ["command"] = false,-- true = the book is part of the menu opened with Config.CraftingCommand. false = the book opens by item (or by coords, if any are set).
    ["label"] = "Cocaine",-- Display name of the crafting book.
    ["coords"] = {-- Crafting locations. //You can add multiple crafting points. Empty table = no locations.
        {1777.6832275390625, -651.0060424804688, 42.6624870300293},
        {-2257.0810546875, -1912.63330078125, 117.27413940429688},
        {-157.4902801513672, 1491.6593017578125, 116.1640853881836}
    },
    -- Available crafting recipes.
    ["craftItems"] = {
        {
            ["time"] = 5, -- Crafting duration in seconds.
            ["items"] = {-- Required items for crafting.
                { ["item"] = "Heroin_Powder", ["count"] = 2, ["label"] = "Heroin Powder" }, 
                { ["item"] = "Injection", ["count"] = 1, ["label"] = "Empty Injection" }
            },
            ["weapon"] = false, -- Weapon output. // false = normal item. true = ["item"] is a weapon name.
            ["count"] = 1, -- Amount of crafted items.
            ["professionExp"] = 5,-- Profession experience gained.
            ["category"] = false,-- Crafting category. // false = no category.
            ["discovery"] = {-- Discovery system. Use ["discovery"] = false (or leave the key out) for a recipe everyone knows.
                ["blueprint"] = "Heroin_Blueprint",-- Blueprint item required for unlocking. (false is disabled) > string = "Item" for learn
                ["self"] = true -- Self Discovery is a feature that allows players to experiment with hidden recipes. (can put here items and try if get right or bad recipe)
            },         
            ["metadata"] = {-- Metadata added to crafted item. -- nil or {} = no metadata. One entry per value.
                { ["type"] = "quality", ["value"] = 100 },
                { ["type"] = "durability", ["value"] = 100 }
            },
            ["item"] = "Heroin_Injection",-- Created item.
            ["grade"] = false,-- Required job grade (minimum). // false = no grade restriction
            ["description"] = "A prepared heroin injection created using heroin powder and an empty injection.",-- Recipe description displayed in UI.
            ["label"] = "Heroin Injection",-- Display name.
            ["propHash"] = {-- Objects used during crafting. hash = prop model, id = item whose image is shown, name = text shown. {} = no prop needed.
                {["name"] = "Wooden Table", ["hash"] = "p_table11x", ["id"] = "Camping_5" }
            },           
            ["profession"] = {"Drug Manufacturing", 3}, -- Required profession. // Format: {"Profession Name", Level}. false = no profession.
            ["hideRecipe"] = false,-- Hide recipe if requirements are not met (grade / profession). -- true = hidden, false = visible.
            ["categoryImage"] = "",-- Category image from inventory icons (file name without .png). // Empty string = no image.
            ["giveBack"] = { -- Items returned after crafting. -- false/nil = no returned items.
                {["item"] = "Injection", ["count"] = 1 }
            }
        }
    }
}
```

Notes:

- A book opens by item only when `["command"]` is `false` and `["coords"]` is empty. The usable item is the book key (`"Cocaine"` above), so the key has to be a real item name. The example above has coordinates, so it opens at those points and the item is not registered.
- `["discovery"]` locks the recipe until the player learns it. A table with both `["blueprint"] = false` and `["self"] = false` still locks the recipe, with no way to learn it. For a normal recipe use `["discovery"] = false`.
- Self discovery is offered in books opened by item or by coordinates. In the command menu a locked recipe can only be learned with its blueprint item.
- Blueprint items are registered as usable items by the script. They still have to exist in your inventory items.
- The profession name has to exist in `Config.Profession` in `config.lua`.
- The player has to be within 2 meters of one of the props in `["propHash"]` to craft.
- Restart `gum_craft` after you change the file.
