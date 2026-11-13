# Teleportation Houses and Interior Shells

## Introduction

This document explains how to create and set up teleportation houses and interior shells in the game. This system allows you to easily manage teleports to specific locations under the map and create interiors for your houses with various models.

## Step 1: Creating an Interior Shell

An interior shell is a file of models that defines the look of the interior of your house. You can define the main interior model and sub-models to expand the structure.

1. Open the `configInteriorPreset.lua` file.
2. Add the following structure for your interior model:

```lua
["int3"] = {  -- Model Name
    ["Page"] = 1,  -- Page
    ["Interior_Model"] = "int3",  -- Main model
    ["House_Offset"] = {0.19, 1.20, 0.77},  -- Model offset (position)
    ["Interior_SubModel"] = {  -- Sub-model definitions
        "int3Test",  -- Sub-models
        "int3Test2",
    },
}
```

- **["Page"]**: The page where the model is located.
- **["Interior_Model"]**: The main interior model.
- **["House_Offset"]**: The position offset of the model (adjust this if needed).
- **["Interior_SubModel"]**: Additional sub-models used for the interior.

## Step 2: Creating the Interior

1. To create the interior, use the following command:

```
/createInterior int3  -- Replace 'int3' with the model name for the interior you want to create
```

2. Use the noclip mode to reach the entrance/exit point (inside the house, near the doors).

3. Once you're at the entry point, run the following command to get the offset:

```
/getOffset
```

You should get the position values (e.g., `{x, y, z}` coordinates) that you will use to fine-tune the model placement.

4. After obtaining the offset values, you can update your configuration with the new values.

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
}
```

## Step 3: Teleportation House Preset

Teleportation houses are simpler to set up. You only need to define the coordinates and rotation of the door to a specific location under the map.

### Steps:

1. Open `configTeleportationPreset.lua`.

2. Add the following structure, defining the teleportation location:

```lua
["interiorModelName"] = {--Some teleportation houses do not have a model. For example, you can write here "Tleeportublackwater" practically doesn't matter what is here.
    ["Interior_Model"] = "",
    ["DoorCoords"] = {-807.49, -1500.8, 42.55, 68.81}  -- {x, y, z, heading}
}
```

- **["DoorCoords"]**: Coordinates and rotation (x, y, z, heading) of the door leading to the teleport location.

3. Replace `interiorModelName` with the desired interior model name, and set the coordinates for the teleportation door.

### 4. Final Steps
- The interiors is now ready for use.
- **Don't forget to add an image** in `gum_housing/pages/` with the model’s name (`int3.png`).
