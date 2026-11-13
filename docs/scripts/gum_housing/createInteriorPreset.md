# Teleportation Houses and Interior Shells

## Introduction

This document explains how to create and set up teleportation houses and interior shells in the game. This system allows you to easily manage teleports to specific locations under the map and create interiors for your houses with various models.

Both kinds of preset are pages of the interior book (`Interior_Book`) and both go in the same table, `Config.InteriorPresets` in `config/configInteriorPreset.lua`. The script decides between them by `["Interior_Model"]`: a model name means a shell is spawned, an empty string means the player is teleported to `["DoorCoords"]`.

The commands on this page only exist while `Config.Debug = true` in `config/config.lua`.

## Step 1: Creating an Interior Shell

An interior shell is a file of models that defines the look of the interior of your house. You can define the main interior model and sub-models to expand the structure.

1. Open the `config/configInteriorPreset.lua` file.
2. Add the following structure for your interior model inside `Config.InteriorPresets`:

```lua
["int3"] = {  -- Model Name
    ["Page"] = 1,  -- Page
    ["Interior_Model"] = "int3",  -- Main model
    ["House_Offset"] = {0.19, 1.20, 0.77},  -- Model offset (position)
    ["Interior_SubModel"] = {  -- Sub-model definitions
        "int3Test",  -- Sub-models
        "int3Test2",
    },
},
```

- **["Page"]**: The page where the model is located. Pages are sorted by this number, so give every preset its own number.
- **["Interior_Model"]**: The main interior model.
- **["House_Offset"]**: The position of the entrance inside the shell, as an offset from the model (adjust this if needed).
- **["Interior_SubModel"]**: Additional sub-models used for the interior. This key is optional. The `int3` preset that ships in the config does not have it.

## Step 2: Creating the Interior

1. To create the interior, use the following command:

```
/createInterior int3  -- Replace 'int3' with the model name for the interior you want to create
```

The model is spawned at your position.

2. Use the noclip mode to reach the entrance/exit point (inside the house, near the doors).

3. Once you're at the entry point, run the following command to get the offset:

```
/getOffset
```

The offset is copied to your clipboard as a finished config line. Paste it with CTRL+V:

```lua
["House_Offset"] = {0.0728759765625, 1.3671875, 0.77396392822265},
```

4. Replace the `["House_Offset"]` line in your preset with the pasted line.

Example after running `/getOffset`:

```lua
["int3"] = {
    ["Page"] = 1,
    ["Interior_Model"] = "int3",
    ["House_Offset"] = {0.0728759765625, 1.3671875, 0.77396392822265},
    ["Interior_SubModel"] = {
        "int3Test",
        "int3Test2",
    },
},
```

## Step 3: Teleportation House Preset

Teleportation houses are simpler to set up. You only need to define the coordinates and rotation of the door to a specific location under the map.

### Steps:

1. Open `config/configInteriorPreset.lua`. Teleportation presets go in the same `Config.InteriorPresets` table as the shells. There is no separate file for them.

2. Add the following structure, defining the teleportation location:

```lua
["interiorModelName"] = {--Some teleportation houses do not have a model. For example, you can write here "Tleeportublackwater" practically doesn't matter what is here.
    ["Page"] = 2,
    ["Interior_Model"] = "",
    ["DoorCoords"] = {-807.49, -1500.8, 42.55, 68.81}  -- {x, y, z, heading}
},
```

- **["Page"]**: The page in the interior book. It is required here too.
- **["Interior_Model"]**: Leave it as an empty string. That is what makes the preset a teleport.
- **["DoorCoords"]**: Coordinates and rotation (x, y, z, heading) of the door leading to the teleport location. Players arrive here and leave from here.

3. Replace `interiorModelName` with the desired name, and set the coordinates for the teleportation door. The name is the preset key, and the page image is looked up by it.

The config ships with four teleport presets for Blackwater rooms (`blk_1fl_1br`, `blk_2fl_2br`, `blk_2fl_3br_1`, `blk_2fl_3br_2`) that you can use as a pattern.

The last entry in the file, `["Empty"]`, only has a `["Page"]`. It is drawn as a blank page with no Build button.

### 4. Final Steps
- The interiors is now ready for use.
- **Don't forget to add an image** in `gum_housing/pages/interior/`, named after the preset key (`int3.png`). The book shows this image as the page.
- Restart `gum_housing` so the new preset is loaded.
