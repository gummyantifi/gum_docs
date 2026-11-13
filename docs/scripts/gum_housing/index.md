---
icon: house-chimney
---

# GUM Housing: install and configure

This page covers the resource named `gum_housing`. Houses, built houses (shells raised in stages from materials), teleport interiors, hotel rooms, furniture, gates, animals, job houses with grades and salaries, and a furniture store all live in this one resource.

Related pages:

- [Creating houses for presets](createHousePreset.md)
- [Teleportation houses and interior shells](createInteriorPreset.md)
- [Creating houses and interiors from presets in the game](createLandOrHouse.md)
- [Troubleshooting](troubleshooting.md)

Video guide for the preset tools: [Script Usage Video Guide](https://youtu.be/QnCAQtMmEM8)

## Requirements

- `gumApi`. Every client and server file gets its API through the `getApi` event, so gumApi has to start before `gum_housing`.
- `oxmysql`. All database access goes through `exports.oxmysql`.
- Optional: `gum_stables`. The stable menus in a house only load horses and wagons when `gum_stables` is started.
- Optional: a clothing script. The wardrobe prompt fires the event named in `Config.ClotheEvent` (default `gum_clothing:checkOwnedClothe`).
- Optional: a bank script for job salaries. See `Config.BankAcountPay` below.

The `fxmanifest.lua` has no `dependency` lines, so the start order in your server config is what matters:

```
ensure oxmysql
ensure gumApi
ensure gum_housing
```

## Install

1. Put the folder in your resources as `gum_housing`. The UI loads its book pages from `nui://gum_housing/...`, so do not rename the folder.
2. Start it after `oxmysql` and `gumApi`.
3. Do not import any SQL. On start the script creates its four tables if they are missing: `housing`, `housingcreated`, `housingint` and `hotels`. It also adds newer columns to existing tables on every start.
4. Add the items (next section).
5. Translate `config/configLanguage.lua` and set `Config.Debug` in `config/config.lua` for your stage (see below).

### Items

The script registers these items as usable. They must exist in your inventory's item list.

| Item | Set in | What it does |
| --- | --- | --- |
| `Build_Book` | `Config.Build_Book` in `config/config.lua` | Opens the house book. |
| `Interior_Book` | `Config.Interior_Book` in `config/config.lua` | Opens the interior book. |
| `Lockpick` | `Config.Lockpick` in `config/config.lua` | Used up when a player lockpicks a door. Set it to `nil` to turn lockpicking off. |
| every `itemId` in `config/configFurniture.lua` | `Config.Furniture`, `Config.Gate`, `Config.animals` | Using the item starts placing that furniture, gate or animal. |

The furniture `itemId` values look like `Furniture_1`, `Furniture_Gate_1`, `Furniture_Chicken`. Item names are case sensitive, so the name in your item list has to match the config exactly.

`Config.AutoInsertSql` (default `false`) inserts the furniture, gate and animal items into a table named `items` when the resource starts. It writes the columns `item`, `label`, `limit`, `can_remove`, `type` and `usable`. It does not insert `Build_Book`, `Interior_Book` or `Lockpick`. Add those by hand.

The build materials and storage upgrade materials named in `Config.Presets` and `Config.Upgrade_Items` are also items from your own item list.

## Debug mode

`Config.Debug` is in `config/config.lua`. In the source it is `true`.

With `Config.Debug = true`:

- The preset commands `/createHouse`, `/createProp`, `/createInterior` and `/getOffset` exist. They are not registered at all when debug is off.
- The client asks the server for the house data 3 seconds after the resource starts, so you can restart `gum_housing` and keep testing without logging in again.
- Client and server print what they do to the console.

Set `Config.Debug = false` on a live server.

## Config files

All config files are in `gum_housing/config/` and are not escrowed.

| File | What is in it |
| --- | --- |
| `config.lua` | Main settings, job permissions, hotel rooms, and the fixed house list `Config.Houses`. |
| `configSrv.lua` | Server only: Discord webhook settings in the `ConfigSrv` table. |
| `configLanguage.lua` | All texts, prompt labels and blip settings in `Config.Language`. |
| `configFurniture.lua` | Furniture store locations, furniture, gates, animals. |
| `configHousePreset.lua` | `Config.Presets`: the pages of the house book. |
| `configInteriorPreset.lua` | `Config.InteriorPresets`: the pages of the interior book. |

### config.lua

Commands and permissions:

- `Config.CommandForManageHouse` (default `"house"`): the command an owner or key holder uses to open the management menu.
- `Config.ForAdminDebugInfo` (default `"houseinfo"`): the command that shows house IDs on screen.
- `Config.Jobs`: what each job may do. Each job has `openBook`, `buildViaBook`, `buyHouse`, `buildHouse`, `showInfo`, `houseList`, `removeOwner` and `deleteHouse`. Players whose job is not listed get the `["Unemployed"]` entry, so keep that key.
- `Config.UnemployedJob` (default `"Unemployed"`): the job a player is set to when they lose a job house.
- `Config.HouseListCoords`: where the house list prompt appears for jobs with `houseList = true`.

Money and limits:

- `Config.DaysForRent` (default `7`): days between rent or tax checks.
- `Config.MaxRentPrepay` (default `4`): how many periods a player can prepay.
- `Config.GiveBackMoneyTax` (default `true`) and `Config.TaxBackMoney` (default `50`): what a player gets back when selling a house. See the troubleshooting page.
- `Config.MaxHouseOnPlayer` (default `2`), `Config.MaxFractionHouses` (default `10`), `Config.MaxLimitForAll` (default `false`): house limits per player.
- `Config.BankAcountPay` (default `true`): job salaries are paid through `setBankData` in `server/serverBank.lua`, which triggers the server event `gum_bank:getPayment`. With `false`, salaries are added as cash through gumApi.
- `Config.RemoveFurnitureAfterAutoSell` and `Config.RemoveFurnitureAfterManualSell` (both `true`): clear what was placed in the house when it is lost for non-payment, or sold by the owner.

Storage and furniture:

- `Config.EnchantedStorage` (default `true`): furniture with `isStorage` works as a storage unit.
- `Config.DisableOldStorage` (default `true`): turns off the older storage spots placed from the management menu.
- `Config.LimitFurniture` (default `300`): furniture limit per house. `0` means no limit.
- `Config.Upgrade_Items`: materials for each storage upgrade level.

Other:

- `Config.ClotheEvent` and `Config.ServerClothing`: the event for the wardrobe prompt. With `Config.ServerClothing = true` it is sent as a server event, with `false` (default) as a client event. The resource's `readme.md` names `rsg-clothes:OpenOutfits` as the client event to use on RSG.
- `Config.Lockpick` (default `"Lockpick"`): the lockpick item. One is removed per attempt.
- `Config.SaveDoorsToSQL` and `Config.SaveDoorsToSqlOnlyJob` (both `false`): save door lock state to the database.
- `Config.DisableBlipForBuyed` (default `true`).
- `Config.OpenCloseLog` and `Config.OpenCloseLink`: the "Discord notify" button in the management menu and its webhook.
- `Config.Hotels`: hotel rooms by town, each with `Name`, `MainDoor`, `Clothe`, `Storage`, `InCoords`, `OutCoords`, `ExitDistance`, `Price` and `StorageSize`.
- `Config.Houses`: houses defined in the file. Houses made in game with the books are stored in the database, not here.

### configSrv.lua

`ConfigSrv.discordHook` (default `false`), `ConfigSrv.webhookLink`, `ConfigSrv.webhookColor` and `ConfigSrv.webhookLanguage`. The file has to start with `ConfigSrv = {}`. Replace the webhook link with your own before you turn the hook on.

### configFurniture.lua

- `Config.RenderFurniDistance` (default `100.0`): render distance for objects without their own `rDistance`.
- `Config.FurniStore`: store locations with `blipSprite`, `blipName`, `jobData`, `furnitures`, `animals`, `gates`, `storeCoords`, `propCoords` and `camCoords`.
- `Config.Furniture`, `Config.Gate`, `Config.animals`: one entry per model with `itemId`, `label`, `price` and optional `rDistance`. Furniture can have `isStorage`. Gates have `isBig`.
- `Config.OffsetSwitch`, `Config.BarnDoors`, `Config.DisableCollision`: door and collision exceptions.

## Commands

| Command | Who | What it does |
| --- | --- | --- |
| `/house` | Owner or key holder of the house or interior | Opens the management menu. Name set by `Config.CommandForManageHouse`. |
| `/houseinfo` | Jobs with `houseList = true` | Shows house IDs on screen. Name set by `Config.ForAdminDebugInfo`. |
| `/reloadDoors` | Anyone | Rebuilds the door list of the house you are at. 5 minute cooldown. The name is fixed in the code. |
| `/houseLoad` | Anyone | Requests the house data from the server again. 60 second cooldown. |
| `/createHouse`, `/createProp`, `/createInterior`, `/getOffset` | Only with `Config.Debug = true` | Preset tools. See the preset pages. |

## Hooks for your own scripts

- `sendMoneyAnywhere(id, transType, value, playerSrc)` in `server/serverBank.lua` is an empty function the script calls when money is paid for a house. `transType` is `buyhousebyprice`, `buyhousebytax` or `buyhousebyrent` (with the player source), or `tax` or `rent` for the periodic payments. Fill it in if that money should go somewhere, for example a town treasury.
- `setBankData(source, steam, charid, money)` in the same file pays job salaries when `Config.BankAcountPay` is `true`.

## Teleporting between houses

- To teleport between houses, move out of the current house's range.
- Use the teleport function to move to the desired house.

## Customers also ran into these

Housing has its own page for this: [GUM Housing: troubleshooting](troubleshooting.md).
