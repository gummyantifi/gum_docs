
# Horse Coloring and Customization Guide

## 1) Enabling Debug Mode
Before you start, make sure that the debug version is enabled:
```lua
debug = true
```

## 2) Entering the Horse Coloring Menu
Once debug mode is enabled, navigate to the horse coloring menu.

## 3) Adjusting the Horse Scale
- If you want to change the horse's size, adjust the "scale" slider in the menu.
- Open the console [F8] and note down the "scale" value displayed.

## 4) Coloring the Horse and Mane
- When applying colors, the console [F8] will display logs such as "Horse" or "Tails".
- Note down the numerical values that appear and use them accordingly.

## 5) Adding a Custom Horse Entry
To define a new horse model with custom colors, add an entry in the following format:

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
    horseScale = 1.1, -- Use the "scale" value from [F8]
    fireHooves = false,
    horseStats = nil,
    canSaveWild = true,
    canSellWild = true,
    blackPrice = 0,
},
```

### Notes:
- Replace `A_C_Horse_AmericanPaint_Greyovero` with the appropriate horse model name and add a number if creating a variant.
- Only add numerical values for colors.
- Ensure you copy the "scale" value accurately from the console.

This guide should help you set up and customize horses properly in your script. Enjoy your horse customization!

