---
icon: wrench
---

# GUM Housing: troubleshooting

This page lists housing problems that customers have already hit, with the cause and the fix. For install and config, see [GUM Housing: install and configure](index.md).

## Check these first

1. gumApi is installed, up to date, and starts before `gum_housing`. `oxmysql` starts before both. See the gumApi troubleshooting page.
2. After every update, add the new config keys and language lines from the changelog to your existing files. A missing key or language line shows up as an `attempt to index a nil value` error. The list at the end of this page has the ones added so far.
3. Replace every file the changelog entry names, client files included.
4. On a first install, check your database for tables named `housing`, `housingcreated`, `housingint` or `hotels` left over from another script, and drop them before you start. The script creates these four tables itself on startup and does not touch a table that already exists under the same name, apart from adding its newer columns.
5. Make database changes with the server stopped, and check the rows you edit by hand for `NULL` values.

## Problems and fixes

### After an update

#### "attempt to index a nil value (field 'PlotBoundary')" after updating

It shows up in two forms: as a client error when the "View plot" prompt is used, and as `error during NUI callback buildThisHouse`, which stops builders from placing or fencing a house.

Cause: the update that added the plot view needs a config block that configs carried over from an older version do not have.

Fix: add this block to your housing config:

```lua
Config.PlotBoundary = {
    enabled = true, -- Show the toggle in property overview and staff management.
    height = 3.5, -- Wall height above its shifted base, in meters.
    zOffset = -1.25, -- Base is 1 meter lower than before; the extra height preserves the top edge.
    drawDistance = 150.0, -- Only draw wall segments this close to the player, in meters.
    color = {210, 235, 255, 45}, -- Red, green, blue, opacity (0-255).
    edgeLines = true, -- Thin top/bottom outlines make the boundary easier to read.
}
```

Also add these two lines to the language table in `config/configLanguage.lua`:

```lua
    buttonShowPlot = "View plot",
    buttonShowPlotKey = 0x760A9C6F,
```

#### The pay tax, pay rent, or give keys prompt opens behind the housing menu

Cause: an outdated housing version. Update `gum_housing`, and update gumApi as well.

#### Error when editing furniture that is already placed (draw text)

Cause: missing language lines. The edit mode reads these from `Config.Language` in `config/configLanguage.lua`:

```lua
    drawTextChangeMode = "[V]",
    drawTextEndMode = "[ESC]",
    drawTextArrowUp = "[↑]",
    drawTextArrowDown = "[↓]",
    drawTextArrowLeft = "[←]",
    drawTextArrowRight = "[→]",
    drawTextRKey = "[R]",
    drawTextFKey = "[F]",
```

Fix: add the lines and replace all client files.

#### Console says `configSrv.lua` cannot be found after paying taxes

Cause: `config/configSrv.lua` is listed in the `fxmanifest.lua` server scripts but was missing from the install.

Fix: put `configSrv.lua` from the current download in `gum_housing/config/`. Its first line must be:

```lua
ConfigSrv = {}
```

#### Warning: sending large event gum_housing:getHouses

```text
Warning: sending large event gum_housing:getHouses (2578556 bytes). This may cause performance issues. Consider using latent events instead.
```

Cause: an old version. The current server file sends this event as a latent event. Update the client and server files.

#### Wardrobes with storage stopped working as storage

This applies when you turn on storage inside furniture (`Config.EnchantedStorage = true`). Wardrobes that were already placed are not recognized as storage units.

Fix: players pick up each wardrobe that has storage and place it again.

#### Wagons at house stables spawn facing the wrong way

An update fixed wagons spawning at a fixed rotation. Players need to set their stable position again once after that update ("Stable position" in the management menu).

### Database and ownership

#### Houses disappear after a server restart, with a duplicate house error on startup

Cause: old or duplicated housing rows in the database. The server cache also keeps some housing ownership data.

Fix, in this order:

1. Stop the server. Database changes cannot be made while it is live.
2. Drop all four housing tables: `housing`, `housingcreated`, `housingint` and `hotels`.
3. Clear the server cache, including the `db` folder.
4. Start the server. The script creates its tables again on startup.

Dropping the tables removes the houses stored in them, so this is the route for a fresh install or for a database that still holds tables from another housing script.

#### The buy button is missing on every house, and doors no longer lock

In this case the server console also showed `attempt to compare number with nil`, and only hotel rooms could still be bought.

Cause: one house row in the database had a `NULL` value entered by hand. That single row broke buying and door locking for all houses.

Fix: find the row with the `NULL` value and correct it.

#### Tax or rent is not deducted because I do not use vorp_banking

In the current code, tax and rent do not go through a bank script. The owner prepays in the management menu, the amount is taken from their cash or gold through gumApi, and the periodic tax or rent is then deducted from that prepaid balance every `Config.DaysForRent` days. If the balance is too low, the house is released.

The bank is only used for job salaries. With `Config.BankAcountPay = true`, salaries go through `setBankData` in `server/serverBank.lua`, which triggers the server event `gum_bank:getPayment`. If you use a different bank script, point that function at it, or set `Config.BankAcountPay = false` to pay salaries as cash.

#### Selling a house returns a few dollars and not the percentage

The owner only gets money back when the house has a fixed price and this is set in `config/config.lua`:

```lua
Config.GiveBackMoneyTax = true
```

A fixed price house without tax returns the full price. A fixed price house with tax returns `Config.TaxBackMoney` percent of the price (default `50`). If `Config.TaxBackMoney` is missing from an older config, add it.

### Doors

#### A door on a book-built house swings the wrong way when locked

Cause: the door's rotation in the house preset. A door that sits open by default needs adjusting.

Fix: open `config/configHousePreset.lua`, find the preset that was built, find that door in its `["Custom_Models"]` list, and add or remove 90 on the last value (the heading).

#### Doors worked and then stopped working

Use the `/reloadDoors` command while at the house. It rebuilds the door list of the property. It has a 5 minute cooldown, and inside the cooldown you get the message "Now u cant recreate doors. You need wait."

The command name is fixed. The config has a line `Config.ForReloadDoorsData = "reloadDoors"`, but changing it does not rename the command in the current code.

If owners of built houses cannot lock or unlock at all, update the client files. An update fixed a wrong lock state being returned for constructed houses.

### Setup questions

#### Furniture items give 404 image warnings in the inventory

The housing script does not include images for furniture items. This is expected.

#### Furniture items do not work on RSG

Check the item names. The config uses names with a capital letter, such as `Furniture_1`, `Build_Book`, `Interior_Book` and `Lockpick`. If your item list uses lowercase names (`furniture_1`), either rename the items or change the `itemId` values in `gum_housing/config/configFurniture.lua` and the item names in `gum_housing/config/config.lua` to match.

#### We moved from VORP to RSG. Do we need a different housing version?

No. The same script works on both. Housing asks gumApi which framework is running, so the change is made in gumApi.

#### Which houses come with the script?

One test house and one test interior. The house presets in the config are made for a separate house pack (SPOONI's pack, which the resource's `readme.md` also points to). You can buy only some of those houses, or use models from another creator and set them up yourself.

#### Can taxes be charged every 30 days?

Yes. Set `Config.DaysForRent = 30` in `config/config.lua` (default `7`). The buy prompt texts `promptHouseInfoBuy2` and `promptHouseInfoBuy3` in `config/configLanguage.lua` say "every week", so change them too.

#### Which job grade does the buyer of a job house get?

The buyer of a job house is given the house's job through `setJob` in gumApi. That is the place to change it if you use a different job system.

## Config keys and language lines an old install may be missing

Updates added these. If your config files are older than the script files, compare them against the files in the current download and add what is missing.

In `config/config.lua`:

```lua
Config.EnchantedStorage = true -- storage inside furniture
Config.DisableOldStorage = true -- turn off the old storage spots
Config.GiveBackMoneyTax = true
Config.TaxBackMoney = 50
Config.RemoveFurnitureAfterAutoSell = true
```

In `Config.Jobs`, every job needs `buildViaBook = true` or `false`. It decides whether the job can use the Build button in the house book.

In `config/configFurniture.lua` (moved here from `config.lua`):

```lua
Config.RenderFurniDistance = 100.0
```

Furniture entries can have `isStorage` and `rDistance`.

In `Config.Language` in `config/configLanguage.lua`:

- Notifications `[28]` and `[29]`.
- Inputs `[134]` and `[135]`, menu text `[270]`.
- `buttonRenameStorage`.
- `drawTextChangeMode`, `drawTextEndMode`, `drawTextArrowUp`, `drawTextArrowDown`, `drawTextArrowLeft`, `drawTextArrowRight`, `drawTextRKey`, `drawTextFKey`.
- `promptBuilding`, `promptBuildingRotateZP`, `promptBuildingRotateZM`, `promptBuildingRotateXP`, `promptBuildingRotateXM`, `promptBuildingRotateYP`, `promptBuildingRotateYM`, `promptBuildingPlace`, `promptBuildingCancel`, `promptBuildingStopMove`.

For anything not covered here, open a ticket in the GUM Scripts Discord.
