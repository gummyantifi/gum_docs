**# Ranch Creator Guide**

**## 1) Enabling the Ranch Creator**

Before you start, make sure that you have configured the Creator permission in your `Config`:

```lua
Config.AnimalDebug.creatorPermission = {
    command = "ranchcreator",
    groups = {"admin", "superadmin"},
}
```

The `command` defines the command used to open the Ranch Creator.

The `groups` table defines which player groups are allowed to use the Creator.

Example:

```lua
Config.AnimalDebug.creatorPermission = {
    command = "ranchcreator",
    groups = {"admin"},
}
```

You can then open the Creator using:

```text
/ranchcreator
```

**## 2) Opening the Ranch Creator**

After using the configured command, the Ranch Creator interface will open.

The main menu contains:

* **Create New Ranch**
* Existing ranches
* Ranch configuration
* Animal positions
* Trough positions
* Ranch deletion
* Ranch data modification

Press **ESC** to close the Creator.

**## 3) Creating a New Ranch**

Select **Create New Ranch** from the ranch list.

The Creator will clear the current settings and allow you to create a new ranch.

Once the ranch is configured, click:

```text
Create/Save Ranch
```

The ranch will receive a new numerical `key` automatically.

You do not need to manually assign the ranch ID.

**## 4) Basic Ranch Settings**

The **Base Setting** section contains the basic ranch configuration.

### Ranch Disabled

Disables the ranch.

```text
Ranch disabled
```

### Clearing Default NPCs and Animals

When enabled, the script can clear the default NPCs and animals around the ranch.

```text
Clearing default npcs and animals
```

### Ranch Name

Set the name displayed for the ranch.

```text
Set ranch name
```

### Job

Set the job associated with the ranch.

```text
Set job name
```

For example:

```text
rancher
```

### Teleport Animal Coordinates

Set the coordinates used for the animal teleport functionality.

You can click the field while standing at the desired location and the current coordinates will be inserted automatically.

**## 5) Ranch Blip Settings**

The **Blip Setting** section controls the ranch map blip.

### Ranch Blip

Enable or disable the ranch blip.

```text
Ranch Blip
```

### Blip Name

Set the name displayed for the blip.

```text
Set blip name
```

### Blip Sprite

Set the blip sprite ID.

```text
Set blip sprite
```

### Blip Color

Set the blip color ID.

```text
Set blip color
```

**## 6) Ranch Coordinates**

The **Coords Setting** section contains the important ranch locations.

### NPC Model

Set the model used by the ranch purchase NPC.

```text
NPC Model
```

Example:

```text
U_M_M_RHDRancher_01
```

### Buy Coordinates

Set the location where the player can interact with the ranch purchase NPC/spot.

```text
Coords for buy
```

You can simply stand at the desired location and click the field to automatically insert your current coordinates.

### Ranch Center

Set the center of the ranch.

```text
Ranch center
```

This position is also used by the Creator to display the ranch preview area.

### Lead Cancel

Set the location used for cancelling animal leading.

```text
Lead cancel
```

**## 7) Ranch Payment Settings**

The **Pay Setting** section controls ranch rental and purchase prices.

### Gold Pricing

Enable this option if the ranch should use gold instead of dollars.

```text
Gold pricing
```

When disabled:

```text
Rent Price: $100
Purchase Price: $1000
```

When enabled:

```text
Rent Price: G10
Purchase Price: G50
```

### Rent Price

Set the amount required to rent the ranch.

```text
Rent price
```

### Purchase Price

Set the amount required to purchase the ranch.

```text
Purchase price
```

**## 8) Setting Animal Coordinates**

The Creator supports the following animal types:

* Cow
* Chicken
* Pig
* Sheep
* Goat
* Horse

Each animal type has its own coordinate list.

To add a position, click:

```text
Set cow coords
Set chicken coords
Set pig coords
Set sheep coords
Set goat coords
Set horse coords
```

The current player position and heading will automatically be saved.

Example:

```text
-123.45, 456.78, 98.12, 180.00
```

Each coordinate can be removed by clicking the existing coordinate entry.

You can add as many animal positions as required.

**## 9) Setting Trough Coordinates**

Each animal type can also have its own trough.

Available troughs:

* Cow
* Chicken
* Pig
* Sheep
* Goat
* Horse

Click the corresponding trough field while standing at the desired location.

The Creator will automatically insert:

```text
X, Y, Z, Heading
```

Example:

```text
-123.45, 456.78, 98.12, 180.00
```

**## 10) Saving Changes**

Most Creator fields automatically save their current state locally while editing.

When you are ready to save the ranch permanently, click:

```text
Create/Save Ranch
```

This sends the complete ranch configuration to the server and updates the ranch configuration file.

The Creator automatically handles the ranch `key`.

You therefore do not need to manually assign IDs when creating new ranches.

**## 11) Editing an Existing Ranch**

Select an existing ranch from the ranch list.

The Creator will load its current configuration.

You can then modify:

* Ranch name
* Job
* Disabled state
* NPC/animal clearing
* Teleport coordinates
* Blip settings
* NPC model
* Buy coordinates
* Ranch center
* Lead cancel coordinates
* Rent price
* Purchase price
* Gold pricing
* Animal coordinates
* Trough coordinates

Click:

```text
Create/Save Ranch
```

to save the changes.

**## 12) Deleting a Ranch**

Select the ranch you want to remove and click:

```text
Delete Ranch
```

The ranch will be removed from the Creator list and the updated configuration will be sent to the server.

**## 13) Modifying Ranch Data**

The **Modify Ranch** button allows you to edit data belonging to an already existing ranch.

This is different from editing the ranch configuration.

The data editor can display:

* Ranch ID
* Owner
* Ranch money
* Prepaid money
* Animals
* Animal statistics
* Trough statistics

**## 14) Editing Ranch Money**

The data editor allows you to directly modify:

```text
Money
Prepayed
```

Enter the desired numerical value and click outside the field.

The value will be sent to the server and updated.

**## 15) Editing Animals**

The data editor supports:

* Cow
* Chicken
* Pig
* Sheep
* Goat
* Horse

Each animal can be modified individually.

Available values include:

```text
Name
Gender
Age
Hunger
Thirst
Health
Satisfaction
Product
Breedtime
Sleeptime
Exp
Genetics
```

Values can be edited directly by clicking on the corresponding value.

After changing the value, click outside the field to save it.

**## 16) Adding Animals**

Inside the animal data editor, you can add a new animal using:

```text
Add cow
Add chicken
Add pig
Add sheep
Add goat
Add horse
```

The new animal will be created with default values.

For example:

```lua
health = 100,
hunger = 100,
thirst = 100,
satisfaction = 100,
breedtime = 0,
breed = false,
isdead = false,
product = 0,
exp = 0,
age = 0.5,
sleeptime = 0,
genetics = 1,
```

You can then modify the animal values directly through the data editor.

**## 17) Deleting Animals**

Each animal has a delete button:

```text
Type : cow Key: 1 | Delete
```

Click the button to remove that animal from the ranch.

The deletion is immediately sent to the server.

**## 18) Editing Trough Data**

The data editor also allows you to modify individual troughs.

Available values include:

```text
Food
Water
Max capacity
```

For example:

```text
Trough ID: cow
Food: 75
Water: 80
Max capacity: 100
```

Food and water values can be edited directly.

**## 19) Ranch Preview**

When editing a ranch, the Creator displays a preview of the configured ranch positions in the world.

The preview can show:

```text
Ranch center
Animal positions
Animal troughs
Teleport coordinates
Buy NPC/SPOT
Lead Cancel
```

The ranch center is displayed together with the configured ranch information.

The preview also displays:

```text
Name
Job
Clear
Blip Enabled
Blip Name
Blip Sprite
Blip Color
Rent Price
Purchase Price
```

This makes it easier to verify that the ranch has been configured correctly before saving it.

**## 20) Converting an Old Ranch Configuration**

If you are upgrading from an older ranch configuration, the Creator includes an automatic conversion option.

Use the Creator command with:

```text
/ranchcreator convert
```

The Creator will convert the old:

```lua
Config.Ranch_Coords
```

configuration into the newer ranch format.

The converted data includes:

```lua
id
key
job
name
clear
tpCoords
coordsSetting
paySetting
blipSetting
chicken
cow
sheep
goat
horse
pig
trough
```

The converted configuration will then be saved into:

```text
data.json
```

**## 21) Important Notes**

* Make sure your Creator permission groups are configured correctly.
* Only players belonging to an allowed group can use the Creator.
* Coordinates are taken directly from the player's current position.
* Animal positions also save the player's heading.
* Ranch IDs/keys are automatically generated for new ranches.
* Existing ranches can be edited without manually changing their key.
* Always verify the ranch center before configuring the remaining coordinates.
* Make sure the trough coordinates match the corresponding animal type.
* When using the old configuration converter, make sure `Config.Ranch_Coords` contains the old ranch configuration you want to convert.
* The conversion creates/updates `data.json` inside the resource.
* Back up your existing configuration before performing a conversion.

**## 22) Quick Setup Example**

A basic ranch can be created using the following setup:

```text
1. Open the Ranch Creator
2. Select Create New Ranch
3. Set the ranch name
4. Set the job if required
5. Set the ranch center
6. Set the buy NPC/spot coordinates
7. Set the NPC model
8. Configure the blip
9. Configure rent/purchase prices
10. Enable Gold pricing if required
11. Add cow/chicken/pig/sheep/goat/horse positions
12. Set the corresponding trough positions
13. Click Create/Save Ranch
```

After saving, the ranch configuration will be available to the ranch system.

**### Notes:**

The Ranch Creator is designed to allow you to configure the entire ranch directly in-game without manually entering every coordinate into the configuration file.

For the easiest setup, stand at the exact location where you want a coordinate and use the corresponding coordinate button. The Creator will automatically capture your current position and heading.

If you are converting an older ranch configuration, create a backup of your existing files before using:

```text
/ranchcreator convert
```

This gives you an easy way to return to the previous configuration if needed.
