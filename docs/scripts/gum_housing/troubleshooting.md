# GUM Housing: troubleshooting

This page lists the housing problems that support tickets and the changelog have already solved, plus the settings added in updates.

## Check these first

1. gumApi is installed, up to date, and starts after your framework and before `gum_housing`. See the gumApi troubleshooting page.
2. After every update, add the new config blocks and language lines from the changelog to your existing files. The most reported housing error of 2026 (`PlotBoundary`) was one missing config block.
3. Replace every file the changelog entry names. A ticket about broken prompts and a furniture editing error was closed by installing the full latest version, client files included.
4. On a first install, drop any housing tables already in your database before you start the script. The script loads its own SQL on startup.
5. Make database changes with the server stopped, and check the rows you edit by hand for `NULL` values.

## Problems and fixes

### After an update

#### "attempt to index a nil value (field 'PlotBoundary')" after updating

It shows up in two forms: as a client error when the "View plot" prompt is used, and as `error during NUI callback buildThisHouse`, which stops builders from placing or fencing a house.

Cause: the 2026-10-14 update added the plot view (`clientPlotBoundary.lua`) but the config block for it was published a day later as a separate patch, so configs carried over from an older version do not have it.

Fix: add this block to your housing config (changelog 2026-10-15):

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

Also add the two language lines from the 2026-10-14 entry to `Config.Lang`:

```lua
    buttonShowPlot = "View plot",
    buttonShowPlotKey = 0x760A9C6F,
```

#### The pay tax, pay rent, or give keys prompt opens behind the housing menu

Cause in the ticket: an outdated housing version. The prompts worked after the latest version was installed.

Fix: update `gum_housing`, and update gumApi as well. The gumApi update of 2026-06-13 made inputs always display above all other windows.

#### Error when editing furniture that is already placed (draw text)

Cause: the developer read the error as nil values and asked whether the language lines were updated. These lines for the arrows and edit buttons were added on 2026-06-22:

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

Fix: add the lines and replace all client files. The error stopped after the whole update was applied again, client files included.

#### Console says `configSrv.lua` cannot be found after paying taxes

Cause: the file is listed in the `fxmanifest` but was missing from the install.

Fix: make sure `configSrv.lua` from the current download is in place. The developer confirmed in the ticket that its first line must be:

```lua
ConfigSrv = {}
```

#### Warning: sending large event gum_housing:getHouses

```text
Warning: sending large event gum_housing:getHouses (2578556 bytes). This may cause performance issues. Consider using latent events instead.
```

Cause: a version older than 2025-08-19.

Fix: update the client and server files. The 2025-08-19 update resolved it.

#### Wardrobes with storage stopped working as storage

This applies when you move to storage inside furniture (`Config.EnchantedStorage`, added 2025-03-10). Wardrobes that were already placed are not recognized as storage units.

Fix: players pick up each wardrobe that has storage and place it again.

#### Wagons at house stables spawn facing the wrong way

The 2025-08-10 update fixed wagons spawning at a fixed rotation. Players need to reposition their stables once after that update.

### Database and ownership

#### Houses disappear after a server restart, with a duplicate house error on startup

Cause: old or duplicated housing rows in the database. The server cache also keeps some housing ownership data.

Fix, in this order:

1. Stop the server. Database changes cannot be made while it is live.
2. Drop all housing tables. There is more than one.
3. Clear the server cache, including the `db` folder.
4. Start the server. The script creates its tables again on startup.

Dropping the tables removes the houses stored in them, so this is the route for a fresh install or for a database that still holds tables from another housing script.

#### The buy button is missing on every house, and doors no longer lock

In the ticket, the server console also showed `attempt to compare number with nil`, and only hotel rooms could still be bought.

Cause: one house row in the database had a `NULL` value entered by hand. That single row broke buying and door locking for all houses.

Fix: find the row with the `NULL` value and correct it.

#### Tax or rent is not deducted because I do not use vorp_banking

Cause: housing reads and writes bank money through two functions, `getBankData` and `setBankData`, which have to point at your bank script.

Fix: use version 1.0.5 or newer. From that version the file with these functions is open again (not escrowed), so you can edit them for your bank.

#### Selling a house returns a few dollars and not the percentage

The 2025-03-02 update fixed a sale returning, for example, $30 where 30% of the price was expected. The config must have:

```lua
Config.GiveBackMoneyTax = true--If player sell house, he get back money with tax settings
```

### Doors

#### A door on a book-built house swings the wrong way when locked

Cause: the door's rotation in the house preset. A door that sits open by default needs adjusting.

Fix: open `configHousePreset`, find the model of the shell that was built, find that door in it, and add or remove 90 on its rotation.

#### Doors worked and then stopped working

Use the `/reloadDoors` command (added 2025-02-24). It rechecks the doors on the property and recreates them. The command name is set by:

```lua
Config.ForReloadDoorsData = "reloadDoors"
```

If owners of built houses cannot lock or unlock at all, update `clientHouses`. The 2026-04-28 update fixed a wrong lock state being returned for constructed houses.

### Setup questions that came up in tickets

#### Furniture items give 404 image warnings in the inventory

The housing script does not include images for furniture items. Support confirmed this is expected.

#### Furniture items do not work on RSG

Check the item names. In an RSG ticket the developer's first question was whether the items were added as `furniture_1` and not `Furniture_1`. A customer who shared an RSG item list changed the "F" to a lowercase "f" in `gum_housing/config/configFurniture.lua` and `gum_housing/config/config.lua`.

#### We moved from VORP to RSG. Do we need a different housing version?

No. The same script works on both. The developer's answer on 2026-10-18 was to change only the framework in gumApi. Since the gumApi update of 2026-10-20, gumApi no longer needs a framework selected.

#### Which houses come with the script?

One test house and one test interior. The house presets in the config are made for a separate house pack (the developer named SPOONI's pack). You can buy only some of those houses, or use models from another creator and set them up yourself.

#### Can taxes be charged every 30 days?

Yes. Support's answer was to change the rent option in the config from 7 to 30.

#### Which job grade does the buyer of a job house get?

Since 2025-05-22 the buyer of a job house gets the job at grade 0 (before it was 5). The job is set through `setJob` in gumApi, which is where to change it if you use a different job system.

## Settings added in updates

- **2026-10-15**. `Config.PlotBoundary` block (see above). Required by the 2026-10-14 update.
- **2026-10-14**, `clientPlotBoundary.lua`, `fxmanifest`, `clientHouses`. Players can view a plot before buying. New `Config.Lang` lines: `buttonShowPlot = "View plot",` and `buttonShowPlotKey = 0x760A9C6F,`
- **2026-09-19**, `serverHouses`/`serverBank`. New function for sending money from rent payments.
- **2026-07-28**, config only:

```lua
Config.EnchantedStorage = true--This enable storages from furnitures 
Config.DisableOldStorage = true--This disable old storage (from place menu and coords where player stand)
```

- **2026-06-22**, `clientHouses`. Language lines for the arrows and edit buttons: `drawTextChangeMode`, `drawTextEndMode`, `drawTextArrowUp`, `drawTextArrowDown`, `drawTextArrowLeft`, `drawTextArrowRight`, `drawTextRKey`, `drawTextFKey`.
- **2026-06-13**, `serverHouses.lua`. Fixed the automatic SQL insert.
- **2026-04-28**, `clientHouses`. Fixed lock and unlock on constructed houses for land owners.
- **2026-03-15**. `Config.SecondStorage = true--This is second storage for house.`
- **2026-03-09**, all client files. New edit mode: the prop moves along its own axes, and holding a button moves it faster.
- **2026-02-08**, `clientHouses.lua`. New line: `[29] = {"You are not allowed use build house.", 2},`
- **2025-08-19**, client/server. Fixed the large event warning for `gum_housing:getHouses`.
- **2025-08-10**, `clientHouses`. Building permission can be locked for players while they can still view the Build_Book. New line `[135] = {'Accept', 'Enter name for storage'},` and, in jobs, `buildViaBook = true,`
- **2025-06-22**. Custom props removed from the furniture config.
- **2025-05-22**, `clientHouses.lua`, `index.js`, css, html. Furniture counter in `/house`. If you edited the css file, add the `#propLimit` rule from the changelog.
- **2025-05-22**, `serverHouses.lua`. Buying a job house gives the job at grade 0.
- **2025-03-24**, `clientHouses`, `clientInterior`, `serverInterior`. Furniture storage added to interiors.
- **2025-03-10**, client, js, server. New furniture structure with a converter (README and video inside `gum_housing`). New per-object options `isStorage` and `rDistance`. Config changes:

```lua
Config.EnchantedStorage = true
Config.DisableOldStorage = false

from config.lua to > configFurniture
Config.RenderFurniDistance = 100.0
```

  New language lines: `buttonRenameStorage`, `promptBuilding`, `promptBuildingRotateZP`, `promptBuildingRotateZM`, `promptBuildingRotateXP`, `promptBuildingRotateXM`, `promptBuildingRotateYP`, `promptBuildingRotateYM`, `promptBuildingPlace`, `promptBuildingCancel`, `promptBuildingStopMove`.
- **2025-03-04**, `clientHouses.lua`. The sale value is recalculated through `Config.TaxBackMoney`.
- **2025-02-24**, `clientHouses`/`serverHouses.lua`. Commands `/houseload` (testing) and `/reloadDoors`. New lines:

```lua
  Config.ForReloadDoorsData = "reloadDoors"
  [28] = {"Now you can't recreate doors. You need to wait.", 2},
  [134] = {'Accept', 'What do you want to write on Discord?'},
  [270] = "Discord notify",
```

- **2025-02-21**, `server.lua`. `Config.GiveBackMoneyTax = true`
- **2025-02-18**. For storage updates on VORP, replace the VORP file in gumApi's `serverData`.
- **2025-02-14**, `clientHouses.lua`. The maximum distance for furniture generation is now 150.
- **2024-09-19**. `Config.RemoveFurnitureAfterAutoSell = false`. Multiple interior shells for one model type (`clientInterior.lua`).

*Built from the changelog and resolved support tickets. For anything not covered here, open a ticket in the GUM Scripts Discord.*
