# Creating Houses for Presets

## Step 1: Configuring the House Model

1. Open `configHousePreset.lua`
2. Add the following structure, replacing placeholder values as needed:

```lua
["house_model_nya"] =  {
    ["Page"] = 21, -- Define the page
    ["Build_Material"] = {
        [0] = {Wood_Plank = {"Wood plank", 20}, Stone = {"Stones", 20}},
        [1] = {Wood_Plank = {"Wood plank", 30}, Stone = {"Stones", 30}},
        [2] = {Wood_Plank = {"Wood plank", 40}, Stone = {"Stones", 40}},
        [3] = {Wood_Plank = {"Wood plank", 50}, Stone = {"Stones", 50}},
    },
    ["House_Model"] = {"house_model_nya"}, -- Main model and additional ones
    ["Custom_Models"] = {}, -- To be added later
    ["Build_Stage"] = {
        [1] = {
            [1] = {"p_foundationwood01x", 2.2, 2.5, 0.05, 0.0, 0.0, 180.0},
            [2] = {"p_foundationwood01x", 2.2, -2.5, 0.05, 0.0, 0.0, 180.0},
        },
        [2] = {
            [3] = {"p_beecherswall04", 4.7, 2.2, 0.3, 0.0, 0.0, 90.0},
            [4] = {"p_beecherswall04", 4.7, -2.2, 0.3, 0.0, 0.0, 270.0},
            [5] = {"p_beecherswall04", -5.1, 2.2, 0.3, 0.0, 0.0, 90.0},
            [6] = {"p_beecherswall04", -5.1, -2.2, 0.3, 0.0, 0.0, 270.0},
            [7] = {"p_beecherswall02", -2.3, -5.0, 0.3, 0.0, 0.0, 180.0},
            [8] = {"p_beecherswall02", 1.9, -5.0, 0.3, 0.0, 0.0, 360.0},
            [9] = {"p_beecherswall02", -2.3, 5.0, 0.3, 0.0, 0.0, 180.0},
            [10] = {"p_beecherswall02", 1.9, 5.0, 0.3, 0.0, 0.0, 360.0},
        },
        [3] = {
            [11] = {"p_foundationwood01x", 2.2, 2.5, 3.55, 180.0, 0.0, 180.0},
            [12] = {"p_foundationwood01x", 2.2, -2.5, 3.55, 180.0, 0.0, 180.0},
        }
    },
},
```

## Step 2: Creating the House Preset

### 1. Position the House
- Stand on a flat surface (preferably outside the map for convenience).
- Use the command:
  ```
  /createHouse house21
  ```
  *(Replace `house21` with the actual model name.)*
- Adjust the house slightly upwards so that doors and windows align properly.
- **Avoid rotating the model** to simplify the setup.
- Click **Left Mouse Button** `[save and continue]`.

### 2. Adding Doors and Windows
1. Use the command:
   ```
   /createProp p_door01x
   ```
   *(Replace `p_door01x` with the desired prop name.)*
2. Position the prop correctly (e.g., align doors with door frames).
3. Press **ENTER** `[save and continue]`.
4. Copy the generated prop data from the clipboard (CTRL+V to paste).
5. Repeat for all doors and windows.

Example output:
```lua
[1] = {'p_door01x', -0.92, -2.62, -4.57, 0.0, 0.0, 0},
```

### 3. Insert Props into `Custom_Models`
```lua
["Custom_Models"] = {
    [1] = {'p_door01x', -0.92, -2.62, -4.57, 0.0, 0.0, 0},
},
```

### 4. Final Steps
- The house is now ready for use.
- **Don't forget to add an image** in `gum_housing/pages/` with the model’s name (`house_model_nya.png`).

