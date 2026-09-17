**# Ranch Creator Guide**

**## Opening the Creator**

Use:

```text
/ranchcreator 50
```

`50` = Creator distance. Increase it for larger ranches.

For converting the old ranch configuration:

```text
/ranchcreator 50 convert
```

This converts the old `Config.Ranch_Coords` configuration and saves it to `data.json`.

**## Creating a Ranch**

Select:

```text
Create New Ranch
```

Then configure the ranch.

**### Basic Settings**

- **Ranch Name** – Name of the ranch
- **Job** – Job required/used by the ranch
- **Ranch disabled** – Disables the ranch
- **Clearing default NPCs and animals** – Removes default NPCs and animals around the ranch

**### Ranch Coordinates**

You don't need to type coordinates manually.

Simply **stand at the location where you want the position** and click the corresponding field.

- **Coords for buy** – Buy NPC / purchase spot
- **Ranch center** – Center of the ranch
- **Lead cancel** – Lead cancel position
- **Teleport animal coords** – Animal teleport position

The Creator automatically inserts your current coordinates.

**### NPC**

Enter the NPC model you want to use for the ranch purchase spot.

```text
NPC Model
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

Enable **Gold pricing** if the ranch should use gold instead of money.

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

Stand where you want an animal to spawn and click:

```text
Set cow coords
```

or the corresponding animal.

You can add **multiple positions** for each animal.

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

This makes it easy to check the ranch before saving it.

**## Saving**

When everything is ready, click:

```text
Create/Save Ranch
```

New ranches automatically receive a `key`.

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

Animal values include:

```text
Name
Gender
Age
Hunger
Thirst
Health
Satisfaction
Product
Sleeptime
Exp
Genetics
```

You can also add or delete animals directly from this menu.

**### Quick Setup**

```text
/ranchcreator 50
```

1. **Create New Ranch**
2. Set the ranch name/job
3. Stand in the ranch center → click **Ranch center**
4. Stand at the purchase location → click **Coords for buy**
5. Set the NPC model
6. Set the remaining coordinates
7. Add animal positions
8. Set troughs
9. Configure blip and prices
10. Click **Create/Save Ranch**

**### Notes:**

- For coordinates, **stand where you want the position and click the field**.

- You can add multiple animal positions.

- Use a larger distance in `/ranchcreator 50` for larger ranches.

- Press **ESC** to close the Creator.
