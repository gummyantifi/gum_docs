## Manual Crafting Configuration

If you don't want to use **craftingHelper**, you can manually create crafting books and recipes directly inside your configuration.

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
["Cocaine"] = { -- Book key (unique identifier of crafting book)
    ["item"] = "Cocaine",-- Item required to open crafting book. -- nil = no item required, crafting can only be opened from coordinates.
    ["job"] = {"Drugs", "Bandits"},-- Jobs allowed to access this crafting book.
    ["command"] = false,-- Command used to open crafting menu. (false = disabled = use it itemId for open) (true = use Config.CraftingCommand).
    ["description"] = "A powerful stimulant drug that can be used recreationally or in the production of other substances.",-- Description displayed in crafting UI.
    ["label"] = "Cocaine",-- Display item name in crafting book.
    ["coords"] = {-- Crafting locations. //You can add multiple crafting points.
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
            ["weapon"] = false, -- Weapon output. // false = normal item.
            ["count"] = 1, -- Amount of crafted items.
            ["professionExp"] = 5,-- Profession experience gained.
            ["category"] = false,-- Crafting category. // false = no category.
            ["discovery"] = {-- Discovery system.
                ["blueprint"] = "Heroin_Blueprint",-- Blueprint item required for unlocking. (false is disabled) > string = "Item" for learn
                ["self"] = true -- Self Discovery is a feature that allows players to experiment with hidden recipes. (can put here items and try if get right or bad recipe)
            },         
            ["metadata"] = {-- Metadata added to crafted item. -- nil or {} = no metadata.
                ["quality"] = 100, ["durability"] = 100
            },
            ["item"] = "Heroin_Injection",-- Created item.
            ["grade"] = false,-- Required grade. // false = no grade restriction
            ["description"] = "A prepared heroin injection created using heroin powder and an empty injection.",-- Recipe description displayed in UI.
            ["label"] = "Heroin Injection",-- Display name.
            ["propHash"] = {-- Objects used during crafting.
                {["name"] = "Wooden Table", ["hash"] = "p_table11x", ["id"] = "Camping_5" }
            },           
            ["profession"] = {"Drug Manufacturing", 3}, -- Required profession. // Format: {"Profession Name", Level}
            ["hideRecipe"] = false,-- Hide recipe if requirements are not met (grade / profession). -- true = hidden, false = visible.
            ["categoryImage"] = "",-- Category image from inventory icons. // Empty string = no image.
            
            
            ["giveBack"] = { -- Items returned after crafting. -- false/nil = no returned items.
                {["item"] = "Injection", ["count"] = 1 }
            }
        }
    }
}
```