**# Ranch Creator Guide**

**## Two Ways to Create a Ranch**

There are **two ways** to create and configure a ranch:

### 1. Ranch Creator

The recommended and easier way is to use the in-game Ranch Creator.

```text
/ranchcreator 50
```

`50` = Creator distance. You can increase this for larger ranches.

### 2. configRanch.lua

You can also configure ranches manually using `configRanch.lua`.

However, the `configRanch.lua` uses the old/manual configuration format.

After making changes to `configRanch.lua`, you **must convert the configuration** using:

```text
/ranchcreator 50 convert
```

This converts the configuration and saves it to:

```text
data.json
```

So if you modify `configRanch.lua` later, remember to run the convert command again.

**## Opening the Creator**

Use:

```text
/ranchcreator 50
```

The Creator will open if your player group has permission to use it.

Press **ESC** to close the Creator.

**## Creating a Ranch**

Select:

```text
Create New Ranch
```

Then configure the ranch.

**### Basic Settings**

- **Ranch Name** – Name of the ranch
- **Job** – Job used by the ranch
- **Ranch disabled** – Disables the ranch
- **Clearing default NPCs and animals** – Removes default NPCs and animals around the ranch

**### Ranch Coordinates**

All coordinates can be set directly from your current position.

Simply **stand where you want the position and click the corresponding field**.

- **Coords for buy** – Buy NPC / purchase spot
- **Ranch center** – Center of the ranch
- **Lead cancel** – Lead cancel position
- **Teleport animal coords** – Animal teleport position

The Creator automatically inserts your current coordinates.

**### NPC**

Enter the NPC model in **NPC Model**.

Then stand where you want the NPC/buy spot and click **Coords for buy**.

The position will be taken from where you are standing.

**### Blip**

Enable **Ranch Blip** and set:

- Blip Name
- Blip Sprite
- Blip Color

**### Payment**

Set:

- Rent Price
- Purchase Price

Enable **Gold pricing** if the ranch should use gold.

**## Animal Positions**

The Creator supports:

```text
Cow
Chicken
Pig
Sheep
Goat
Horse
```

Animal positions are also taken from your current position.

Stand where you want the animal to spawn and click:

```text
Set cow coords
```

or the corresponding animal.

You can add multiple positions for each animal type.

Click an existing position to remove it.

**## Troughs**

Each animal has its own trough position.

Stand where you want the trough and click:

```text
Cow trough
Chicken trough
Pig trough
Sheep trough
Goat trough
Horse trough
```

The current position and heading will be inserted automatically.

**## Preview**

While configuring a ranch, the Creator shows the configured positions in the world:

```text
Ranch Center
Animal Positions
Troughs
Teleport Position
Buy NPC/SPOT
Lead Cancel
```

This makes it easy to check everything before saving.

**## Saving**

When everything is ready, click:

```text
Create/Save Ranch
```

New ranches automatically receive their own `key`.

**## Editing / Deleting**

Select an existing ranch from the list.

You can edit its settings and save them again with:

```text
Create/Save Ranch
```

To remove it:

```text
Delete Ranch
```

**## Modify Ranch**

**Modify Ranch** is used to edit the actual ranch data.

You can modify:

- Money
- Prepayed
- Animals
- Animal stats
- Trough food/water

You can also add or delete animals directly from this menu.

**### Notes:**

- **All coordinate buttons use your current position** — including the ranch center, buy/NPC spot, animal positions and troughs.

- You can add multiple positions for each animal.

- Use a larger distance in `/ranchcreator 50` for larger ranches.

- If you use `config.lua`, remember to run `/ranchcreator 50 convert` after making changes.

- The `convert` command is only needed when using the manual `config.lua` method.
