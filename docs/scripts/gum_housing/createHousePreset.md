# Creating Houses for Presets

A preset is one page of the house book (`Build_Book`). It names the shell models, the materials for each build stage, and the doors and windows that are attached to the shell.

The commands on this page only exist while `Config.Debug = true` in `config/config.lua`.

## Step 1: Configuring the House Model

1. Open `config/configHousePreset.lua`
2. Add the following structure inside `Config.Presets`, replacing placeholder values as needed:

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

- `["Page"]`: the position in the book. Pages are sorted by this number, so give every preset its own number.
- `["Build_Material"]`: the materials for stages `[0]` to `[3]`. Each entry is `ItemName = {"Label", amount}`, where `ItemName` is the item in your inventory.
- `["House_Model"]`: a list. The presets that ship in the config use up to three models per house, for example `{"house28_int", "house28_ext", "house28-stop-rainfall"}`.
- `["Custom_Models"]`: doors and windows, as `{model, x, y, z, 0.0, 0.0, heading}` relative to the shell.
- `["Build_Stage"]`: the props shown while the house is under construction, grouped by stage.

The first entry in the file, `["existHouse"]` on page 1, is the page for registering a building that already exists on the map. Keep it.

## Step 2: Creating the House Preset

### 1. Position the House
- Stand on a flat surface (preferably outside the map for convenience).
- Use the command:
  ```
  /createHouse house21
  ```
  *(Replace `house21` with the actual model name.)*
- Move the house with the keys shown on the left side of the screen: `R` and `F` for up and down, the arrow keys for X and Y. Adjust the house slightly upwards so that doors and windows align properly.
- This step has no key for rotating the house.
- Click **Left Mouse Button** `[save and continue]`.

### 2. Adding Doors and Windows
1. Use the command:
   ```
   /createProp p_door01x
   ```
   *(Replace `p_door01x` with the desired prop name.)*
   The command only works after the house position was saved with the mouse click.
2. Position the prop correctly (e.g., align doors with door frames). `R` and `F` move it up and down, the arrow keys move it on X and Y, `Q` and `E` rotate it.
3. Press **ENTER** `[save and continue]`.
4. Repeat for all doors and windows. Every time you press ENTER, the full list of saved props is copied to the clipboard again, so you only need to paste once at the end (CTRL+V).
5. Press **BACKSPACE** when you have pasted the list. It removes the house and all props and ends the session, so paste first.

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
- **Don't forget to add an image** in `gum_housing/pages/house/`, named after the preset key (`house_model_nya.png`). The book shows this image as the page.
- Restart `gum_housing` so the new preset is loaded.
