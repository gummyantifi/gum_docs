**# Ranch Creator Guide**

**## Opening the Creator**

Use:

```text
/ranchcreator 50
```

`50` = Creator distance. Increase it for larger ranches.

For converting the old/manual configuration:

```text
/ranchcreator 50 convert
```

This converts the old ranch configuration and saves it to `data.json`.

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

**### NPC Model & Position**

Enter the model you want to use in **NPC Model**.

The NPC position is also taken from your current position when you use the **Coords for buy** field.

So simply:

```text
1. Stand where the NPC should be
2. Enter the NPC model
3. Click Coords for buy
```

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

Animal positions are also taken directly from your current position.

Simply **stand where you want the animal to spawn** and click:

```text
Set cow coords
```

or the corresponding animal.

You can add multiple positions for each animal type.

Click an existing position to remove it.

**## Troughs**

Each animal has its own trough position.

Stand where you want the trough and click the corresponding field:

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

While configuring a ranch, the Creator shows the configured positions in the world.

You can see:

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

You can change its settings and save them again with:

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

**## Using config.lua**

You can also create your ranches manually using the `config.lua` file.

However, manually configured ranches use the old configuration format.

After making changes in `config.lua`, you need to convert the configuration using:

```text
/ranchcreator 50 convert
```

The Creator will convert the configuration and save it to:

```text
data.json
```

**### Quick Setup**

```text
/ranchcreator 50
```

1. **Create New Ranch**
2. Set the ranch name/job
3. Stand in the ranch center → click **Ranch center**
4. Stand where the NPC should be → click **Coords for buy**
5. Set the NPC model
6. Set the remaining coordinates from your current position
7. Add animal positions
8. Set troughs
9. Configure blip and prices
10. Click **Create/Save Ranch**

**### Notes:**

- **All positions are taken from your current position** when using the coordinate buttons — including ranch positions, NPC/buy spot, animal positions and troughs.

- You can add multiple positions for each animal.

- Use a larger distance in `/ranchcreator 50` for larger ranches.

- Press **ESC** to close the Creator.

- If you manually edit `config.lua`, remember to run the **convert command again** after making changes.
