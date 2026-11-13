---
icon: horse-head
---

# GUM Stables: install and configure

`gum_stables` handles horses and wagons: stables, shops, components, coloring, breeding, taming wild horses, training, ageing, horse and wagon storage, company stables, player stores, and a black market. Almost everything is switched on, off, or tuned in the files in the `cfg` folder.

For errors and update steps, see [Troubleshooting](troubleshooting.md). For custom horse colors, see [Horse coloring](horseColoring.md).

## Requirements

- `gumApi`. The script fetches it with the `getApi` event and uses it for player data, money, gold, items, storages, notifications, prompts, and inputs. Start it before `gum_stables`.
- `oxmysql`. All database queries go through `exports.oxmysql`.

`fxmanifest.lua` does not declare dependencies, so the start order in your server config is what counts: `oxmysql`, then `gumApi`, then `gum_stables`.

The script also sends events to other resources. They do nothing if that resource is not installed:

- `gum_target`: `gum_target:addNewTargets` and `gum_target:removeTargets` for stable NPCs.
- `gum_farming`: `gum_farming:checkCartEntity` and `gum_farming:deleteFromFarm` when a wagon spawns or is removed.
- `gum_metabolism`: `gum_metabolism:useMeFromStable`.

## Install

1. Check your database first. The script uses the tables `horses`, `wagons`, `horses_breeding`, `horsestrainers`, `horses_saddles`, `horsesstore`, `wagonstore`, and `real_logic_horses`. If `horses`, `wagons`, or `horses_breeding` are left over from another stables script, drop them (back them up first if you need the data). Their columns do not match.
2. There is no `.sql` file to import. On every start the script runs `CREATE TABLE IF NOT EXISTS` and `ALTER TABLE ... ADD COLUMN IF NOT EXISTS` for everything it needs.
3. Put the resource in your resources folder and start it after `oxmysql` and `gumApi`.
4. Create the items listed below in your inventory, or change the item names in `cfg/config.lua` to items you already have.
5. Translate `cfg/configLang.lua` if needed.
6. Set `debug` in `Config.DebugAndBaseOption` (`cfg/config.lua`). The shipped file has `debug = true`. With debug on, the script prints its events and functions to the console and reloads your stable data when the resource restarts, so you can test config changes with a resource restart. Set `debug = false` on a live server.

Only the `cfg` folder, the `html` folder, `clientCustom.lua`, and `serverCustom.lua` are open for editing. The rest is escrowed.

## Items

The script registers these item names as usable through gumApi. The names are the shipped defaults, each one can be changed in `cfg/config.lua`.

| Item | Config key | Use |
| --- | --- | --- |
| `HorseTag` | `Config.HorseLogic.renameItem` | Rename a horse |
| `CartTag` | `Config.CartLogic.renameItem` | Rename a wagon |
| `Brush` | `Config.HorseLogic.statusSetup.brushItem` | Clean the horse. Loses `brushMetadata` durability per use (`false` turns durability off) |
| `Hoof_Hook` | `Config.HorseLogic.statusSetup.hoofHook.item` | Clean hooves |
| `Horse_Shoe` | `Config.HorseStablesLogic.shoes.shoeItem` | Horseshoes, raise base stats |
| `Repair_Kit` | `Config.CartLogic.statusSetup.repairCartItem` and `repairWheelItem` | Repair a wagon or a wheel |
| `BreederFix` | `Config.HorseStablesLogic.mating.giveMatingToMaleHorse` and `giveMatingToFemaleHorse` | Enable mating on a horse that has it disabled |
| `BreederCut` | `Config.HorseStablesLogic.mating.removeMatingFromHorse` | Remove the mating state |
| `Apple`, `Hay`, `Sugar`, `Wild_Carrot` | `Config.HorseLogic.statusSetup.metabolism.itemsForUse` | Feed |
| `Heal_For_Horse`, `Boost_For_Horse`, `Gold_For_Horse` | same table, `type="stimulant"` | Heal and boost |
| `saddleStand_1`, `saddleStand_2`, `saddleStand_3` | `Config.HorseLogic.transferComponents.saddleStands` | Place a saddle stand |

`Config.HorseLogic.ageingSetup.resetItem` is `nil` by default. Set an item name there if you want an item that resets a horse's age to `resetAgeTo`.

Wagon cargo items are listed in `Config.CartLogic.woodCart` and `stoneCart`. Craftable wagons use the items in each wagon's `craftRecipe` in `cfg/configCarts.lua`. The shipped recipes use item names from the developer's server, so replace them with your own.

## Config files

All files are in the `cfg` folder.

### `config.lua`

- `Config.DebugAndBaseOption`: `debug`, markers (`enableMarkers`, `RGBAMarkers`), blip color swap on open and close (`blipSwapTime`), `defaultUnemployedJob` (`"unemployed"`), and the command names (see Commands).
- `Config.BreederStoreAgeing` (`false`): horses standing in a player store also age.
- `Config.HorseStablesLogic`: stable rules for horses.
  - `callFromStables` (`false`): horses can only be taken out at a stable.
  - `respawnAfterLogin` (`true`): the horse comes back at its last position after reconnect.
  - `whereTheyAreKept`: a horse is only visible in the stable it was saved in, `transferPrice` (`150`) moves it.
  - `wheelChanges` (`false`): numeric inputs instead of wheels in components and coloring.
  - `lightSetting`: night lighting inside stables.
  - `healPrice`, `componentPrices`, `storeHorseAge`.
  - `shoes`: `shoeItem`, `maxLevel`, and wear (`degradeWhileRide`, `degradeWhileLead`, both `0` = off, checked every `degradeEverySecond` seconds).
  - `mating`: price, duration (`time = 57600` seconds), mating chances, the `BreederFix` and `BreederCut` items, `removeMatingFromHorseCmd`.
  - `wildHorses`: `canSaveWildHorses`, `canSellWildHorses`, `cooldownForTameAgain` (`30` minutes), `cooldownForSellTameAgain` (`0` = off), `payForSavePercent`, and the taming minigame (`KeyPress` or `Dragging`, enable only one).
  - `horseBlackmarketDelay` (`60` seconds).
- `Config.HorseLogic`: the horse itself.
  - Whistle and range: `cooldownForAgainWhistle`, `horseWhistleDistance`, `despawnOnRange`, `horseFollowYou`.
  - Storage: `accessToStorage`, `accessOnlyById`, `peltsOnHorse`.
  - `cityCallBlock`: block whistling in the towns in `bannedTowns` (`enabled = false` for horses).
  - `deadHorseCleanup`: delete dead horses `deleteAfterHours` (`168`) real hours after death, once at resource start (`enabled = false`).
  - `ageingSetup`: `realTime`, `ageUpdate`, `maxAge` (`30`), `deadOnOld` (`10` percent), `tebexHorsesAge` (`false`), `resetItem`.
  - `hitchProps`.
  - `statusSetup`: brush, `horseKick`, `horseKickYoung`, train track ragdoll, `dirt`, `metabolism` (hunger and thirst rates, `drinkProps`, `eatProps`, `grazeBlockLocations`, `energySystem`, `itemsForUse`), `hoofHook`.
  - `experienceSystem`: `maxHorseLevel` (`4`), the trick commands, experience rates, `advancedPoints` (training course).
  - `bondingSystem` (including `throwOff`) and `courageSystem`.
  - `transferComponents`: saddle transfer command and `saddleStands`.
- `Config.CartStablesLogic`: `callFromStables`, `respawnAfterLogin`, `despawnOnRange`, storage access, `whereTheyAreKept` (`enabled = false`).
- `Config.CartLogic`: `renameItem`, `cityCallBlock` (`enabled = true` for wagons), `statusSetup` (health saving, damage, `blockStorageWhileDamaged`, wheel breaking, repair items), `componentPrices`, `woodCart`, `stoneCart`.
- `Config.KeySetting`: control hashes for every prompt.

### `configCoords.lua`

- `Config.SpotsForStable`: one entry per public stable. Each has `blipSetting`, `cameraSetting`, `spawnSetting`, `npcSetting` (model, coords, `useTime`, `timeOpen`, `timeClose`, optional `isJobLimited`), `baseSetting` (`enterCoords`, `wildCoords`, `fleeRange`, `quickPick`, `quickPickCub`, `jobLock`), `horsesSetting`, and `cartsSetting` (which menus this stable offers).
- `Config.HorseCompany`: shared stables for a job. `Job` is a job name or a list of job names.
- `Config.PlayerStore` and `Config.EnablePlayerStore`: stores where a job sells horses and wagons to players. The key of each entry is the job name.
- `Config.Blackmarket`: where horses can be sold on the black market. The price is `blackPrice` on the horse.
- `Config.TrainWhitelist`: zones where the train track check is ignored.
- `Config.SimulateNodes` and `Config.SimulateNodesEnabled`: extra spawn points for called wagons where no road is near.
- `Config.enableInteractWithNpc` (`true`): you must look at the NPC to open the stable.

### `configHorses.lua`

`Config.Descriptions` holds the shop text per breed. `Config.Horses` is grouped by breed, and each key is a horse model:

```lua
["A_C_Horse_AmericanPaint_Greyovero"] = {
    enabled = true,
    stables = 0,            -- 0 = all stables, or a stable number, or {1, 2, 3}
    colorName = "Grey",
    dollarPrice = 460,
    goldPrice = 0,
    storageSlot = 20,
    peltStorage = 5,
    jobForBuy = {"unemployed"},
    groupForBuy = {"none"},
    sellPrice = 7,          -- sale value is dollarPrice / sellPrice
    canBreed = true,
},
```

Other per-horse keys: `identifierForBuy` (Steam identifiers allowed to buy, empty allows everyone), `seeIfJobBlock`, `iconState`, `horseBodyHeadColor`, `horseTailManesColor`, `horseScale`, `fireHooves`, `horseStats`, `canSaveWild`, `canSellWild`, `blackPrice`, `maxAge`, `isVip`.

### `configCarts.lua`

`Config.Wagons`, grouped by category, each key a wagon model: `name`, `dollarPrice`, `goldPrice`, `sellPrice`, `storageSlot`, `storageAnimals`, `jobForBuy`, `identifierForBuy`, `groupForBuy`, `isBigCart`, `craftRecipe`, `craftPrice`, `description`, `isVip`.

### `configJobs.lua`

`Config.Jobs` has one entry per job, plus the entry for `Config.DebugAndBaseOption.defaultUnemployedJob`, which applies to everyone and must stay last. Limits: `horseCount`, `cartCount`, `breedCount`, `boostExp`. Permissions: `canManage`, `canTrain`, `canTraining`, `canAdvTraining`, `canTrainFriendly`, `canTrainCourage`, `canTame`, `canSaveTame`, `canSellTame`, `canBreed`, `canShoe`, `canAccess`, `canRepair`, `canRepairWheel`, `canHeal`, `canCleanHoof`, `canSeeStats`, `canDestroy`, `canColorHorse`, `canBuild`, `canBuyCubs`, `canPickSmallHorseForTrain`, `canTransferToPlayer`.

`canTransferToPlayer` is read by the script but is not in the shipped file. Add it to every job that should see the horse "Transfer" prompt.

### `configLang.lua`

`Config.Language` (notifications, menu texts, prompts, inputs), `Config.Suggestions`, and the animal and pelt names (`Config.Animals`, `Config.Pelts`, `Config.HorsePelts`).

### `configSrv.lua`

Server only. `ConfigSrv.playerTransferRadius` (`5.0` meters, the maximum distance between two players for a horse or wagon gift), `ConfigSrv.discordHook`, `ConfigSrv.webhookLink`, `ConfigSrv.webhookColor`, and `ConfigSrv.webhookLanguage` (texts of the Discord log). Put your own webhook in `webhookLink`.

### `full_database.lua` and `purchaseAccess.lua`

`full_database.lua` holds the data tables: color palettes, `Config.propsAttach`, horse accessories, wagon extras, liveries, tints, and propsets. `purchaseAccess.lua` is the logic behind `identifierForBuy`. You normally do not edit either.

### `clientCustom.lua` and `serverCustom.lua`

In the resource root. `checkProtectHorse()` and `checkProtectCart()` decide who may buy a horse or wagon marked `isVip = true`. `protectColoringByVip()` in `clientCustom.lua` can give the coloring permission to players without the `canColorHorse` job permission. All three functions must exist, even if you do not use them.

## Commands

Command names come from `cfg/config.lua`. These are the defaults.

| Command | Who | What it does |
| --- | --- | --- |
| `/trainer` | A job with `canTrain` | Opens the job's administration menu, where players are added to or removed from the job's trainer list. Key: `administrationCommand` (`nil` disables) |
| `/dv` | A job with `canDestroy` | Asks to delete a horse or wagon. Key: `deleteHorseCartCommand` |
| `/checkinv horse 142` or `/checkinv cart 142` | Groups in `checkInventoryGroup` (`admin`, `mod`) | Opens the storage of the horse or wagon with that database id. Key: `checkInventory` |
| `/matingremove` | Everyone | Same as using the `BreederCut` item. Key: `removeMatingFromHorseCmd` |
| `/changeSit` | Everyone, mounted | Changes how you sit on the horse. Key: `Config.HorseLogic.changeSitCommand` |
| `/shotHim` | Everyone, mounted | Plays the hit animation for a hogtied player on the horse. Key: `commandHitPlayer` |
| `/transferSaddle` | Everyone | Moves saddle parts to another horse. Key: `transferComponents.command`, only if `transferComponents.enabled` |
| `/horseRest`, `/horseSleep`, `/horseWallow`, `/horseDrink`, `/horseEat` | Everyone | Horse tricks. The command name is the third value and the required horse level the first value of `horseRest`, `horseSleep`, `horseWallow`, `horseDrink`, `horseEat` in `experienceSystem` |

## Events and exports for other scripts

Send the player's horse or wagon away at once, for example when another script puts the player into an instance. Client side:

```lua
TriggerEvent("gum_stables:fleeHorseInstant")
TriggerEvent("gum_stables:fleeCartInstant")
```

Delete the horse entities of a player from the server:

```lua
TriggerEvent("gum_stables:deleteMyHorseServer", source)
```

Give a horse from the server, for example from a Tebex script. The third argument marks the horse as a Tebex horse (`is_tebex` column):

```lua
TriggerEvent("gum_stables:buyDataSrv", source, horseData, true)
```

`serverCustom.lua` ships a commented example of the `horseData` table. `category` and `colorName` must match a horse in `cfg/configHorses.lua`.

Client export:

```lua
local horsesKept, cartsKept, horseFromStables, cartFromStables, horses, carts = exports.gum_stables:getStables()
```

It returns `whereTheyAreKept.enabled` for horses and for wagons, `callFromStables` for horses and for wagons, and the player's horse list and wagon list.
