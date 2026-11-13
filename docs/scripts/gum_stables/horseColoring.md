
# Horse Coloring and Customization Guide

This guide shows how to find color and scale values in game and save them as a new horse in `cfg/configHorses.lua`.

## 1) Enable debug mode

In `cfg/config.lua`, in `Config.DebugAndBaseOption`:

```lua
debug = true,
```

The color values are only printed with debug on. Turn it off again on a live server.

## 2) Open the horse coloring menu

The "Color" menu of a stable is shown when both are true:

- Your job has `canColorHorse = true` in `cfg/configJobs.lua` (or `protectColoringByVip()` in `clientCustom.lua` returns `true`).
- The stable has `horseColoring = true` in its `horsesSetting` in `cfg/configCoords.lua` (`horseSetting` for a company stable).

## 3) Adjust the horse scale

- Move the "Scale" slider in the menu. Its range is set by `horseScale = {-7, 2}` in `Config.HorseStablesLogic`.
- Open the console [F8]. Each change prints a line that starts with `Scale:`. Note the value.

## 4) Color the horse, mane, and tail

- Set the palette and the three colors for the body, then for the tail and mane.
- When the colored horse is loaded, the console [F8] prints two lines, `Horse Color :` and `Tail Color :`. Each line lists four numbers in this order: palette, color 1, color 2, color 3.
- Note the numbers.

Saving the colors in the menu costs `horseColorPrice` from `Config.HorseStablesLogic.componentPrices` (`0` makes it free).

## 5) Add a custom horse entry

Add a new entry to the breed's table in `Config.Horses` (`cfg/configHorses.lua`):

```lua
["A_C_Horse_AmericanPaint_Greyovero1"] = { -- Add a number to the model name (1,2,3, etc.)
    enabled = true,
    stables = {2, 3, 4},
    colorName = "Grey New",
    dollarPrice = 460,
    goldPrice = 10,
    storageSlot = 20,
    peltStorage = 5,
    jobForBuy = {"unemployed"},
    groupForBuy = {"none"},
    iconState = "state_3",
    sellPrice = 7,
    canBreed = true,
    horseBodyHeadColor = {9, 1, 3, 0}, -- Format: {Palette, Color1, Color2, Color3}
    horseTailManesColor = {2, 1, 2, 3}, -- Format: {Palette, Color1, Color2, Color3}
    horseScale = 1.1, -- Use the "Scale:" value from [F8]
    fireHooves = false,
    horseStats = nil,
    canSaveWild = true,
    canSellWild = true,
    blackPrice = 0,
},
```

### Notes:
- Replace `A_C_Horse_AmericanPaint_Greyovero` with the horse model you colored. The key must be unique, so add a number at the end when you create a variant of a model that is already in the config.
- Give the variant its own `colorName`. The shop and the purchase look a horse up by breed and `colorName`.
- `stables` is `0` for all stables, one stable number, or a list of numbers from `Config.SpotsForStable`.
- Only numbers go into the color tables. Palette `0` is the default palette. `horseBodyHeadColor = nil` and `horseTailManesColor = nil` keep the model's original colors.
- Copy the scale value exactly as printed.

### Video Guide
If this text guide is not enough, you can also check out the video tutorial here:
[Horse Coloring Video Guide](https://www.youtube.com/watch?v=I8KDT0ULBF0)
