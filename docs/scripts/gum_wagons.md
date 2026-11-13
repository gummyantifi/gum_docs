---
icon: wagon-covered
---

# GUM Wagons

GUM Wagons is a wagon management and crafting system. Players build wagons, repair them, hitch draft horses, haul cargo, and trade wagons with each other.

## At a glance

- Video: [GUM Wagons](https://www.youtube.com/watch?v=cZ-cWNTiwb8)
- Store: [GUM Wagons on Tebex](https://gum-scripts.tebex.io/package/7806421)
- Released: 2026-11-12
- Resource name: `gum_wagons`

## What it does

### Wagon construction

- Players build and assemble wagons interactively.
- Crafting materials and requirements are configurable.
- Wheels are installed individually.
- Construction projects are persistent.
- Wagon crafting can be restricted to jobs.

### Damage and repairs

- Wagon health and condition are persistent.
- Wheels take damage and break individually, and wheel damage depends on speed.
- Wheel repair is a minigame with six fasteners.
- Repair kits and repair costs are configurable.

### Draft horses and performance

- Draft horse models and appearances are customizable.
- Horses have Pulling Power, Acceleration, and Braking statistics.
- Horse teams and presets are configurable.
- The combined statistics of the horses affect how the wagon handles.

### Cargo and storage

- Each wagon has its own inventory.
- Wood and coal have specialized transport.
- Cargo capacities are based on weight.
- Hunting wagons store animal carcasses.
- Storage limits and permissions are configurable.

### Customization and trading

- Wagons support extras, tints, liveries, and lanterns.
- Custom wagon components and equipment are supported.
- Players can sell wagons to each other through a marketplace.
- Wagons can be gifted and ownership can be transferred.
- Wagon access can be shared with other players through permissions.

### Other features

- Multiple configurable wagon shops.
- Job and faction wagon management.
- Wagon calling, with the wagon's world position saved.
- Configurable wagon ownership limits.
- Classic and Modern UI themes.
- Developer exports and integration options.

## Before you install

Install **gumApi** first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).

- `fxmanifest.lua` declares two dependencies: `oxmysql` and `gumApi`. Both must start before `gum_wagons`.
- Optional: [GUM Horses](gum_horses.md). The two scripts use the same stable layout and are built to run side by side. GUM Wagons does not need GUM Horses to start.
- Optional: `gum_houses`, for parking wagons at a property.

## Install

1. Put the `gum_wagons` folder in your resources and start it after `oxmysql` and `gumApi`.
2. Do not import any SQL. The script creates its tables on start.
3. Add the items listed below to your inventory.
4. Edit the files in `configs/`, then restart the resource.

### Database tables

The base name is `Config.Database.table` in `configs/configMain.lua` (default `gum_wagons`). It is independent from the `gum_stables` wagons table. The script creates:

| Table | Holds |
| --- | --- |
| `gum_wagons` | Owned wagons, one row each, with the wagon state in the `data` column |
| `gum_wagons_spots` | The open or closed state of each yard |
| `gum_wagons_market_payouts` | Pending payouts from player market sales |

### Items

Item names are case sensitive. These are the defaults.

| Item | Used for | Where it is set |
| --- | --- | --- |
| `Repair_Kit` | Body repair away from a yard, and wheel repair | `Config.WagonManagement.repair.item` and `.wheels.item` |
| `CartTag` | Renaming a wagon | `Config.WagonManagement.rename.item` |
| `Wood`, `Wood_Branch`, `Wood_Bark`, `Wood_Chord`, `Wood_Plank` | Log wagon loads | `Config.Cargo.wood.items` |
| `Stone`, `Charcoal` | Coal wagon loads | `Config.Cargo.coal.items` |
| Crafting ingredients | Building wagons and crafting components | `crafting.ingredients` in `configs/configWagons.lua` and `configs/configComponents.lua` |

The repair and rename items are registered as usable items. Go through the `crafting.ingredients` lists and change the item names to items that exist on your server.

## Configuration

All config files are in `configs/`.

| File | What it holds |
| --- | --- |
| `configMain.lua` | Limits, jobs, calling, database, locations, interaction distances, management actions, wagon status, cargo loads, UI |
| `configSpots.lua` | `Config.Spots`: the wagon yards |
| `configWagons.lua` | `Config.Wagons`: the wagon catalog |
| `configComponents.lua` | `Config.Components` and `Config.DraftHorseComponents`, with crafting recipes |
| `configComponentsWithoutCraft.lua` | The same components without recipes |
| `configDraftHorses.lua` | `Config.DraftHorseHandling` and the draft horse models with their stats |
| `configCargo.lua` | `Config.AnimalCargo` and `Config.LegendaryCargo` |
| `configPlayerWagonMarket.lua` | `Config.PlayerWagonMarket` |
| `configLanguage.lua` | `Config.Language`: text and prompt keys |
| `configServer.lua` | Server only: integration whitelist and Discord webhook |
| `configDebug.lua` | `Config.Debug` switches |

`fxmanifest.lua` loads `configs/configLanguage.lua` (English). A Czech file, `configs/configLanguageCZ.lua`, ships in the folder and is not loaded by default.

### Turn component crafting off

`fxmanifest.lua` loads exactly one component file. To sell components without crafting, replace `'configs/configComponents.lua'` with `'configs/configComponentsWithoutCraft.lua'` in `shared_scripts`. Never load both.

### Main settings (`configs/configMain.lua`)

- `Config.MaxWagons` (500): how many wagons one character can own. `Config.MaxWagonsByJob` sets a different cap per job name.
- `Config.MaxCalledWagons` (2): active wagons per player.
- `Config.Jobs`: per-job permissions, for example `['sheriff'] = {canInspectStorage = true}`. Permissions you leave out are `false`.
- `Config.MustSelectWagon`: players select wagons at the yard before calling them.
- `Config.WagonCalling`: `mode` is `'key'` or `'disabled'`. `control` is the calling key (J by default). A tap calls the last chosen wagon. Holding for `holdMs` opens a radial menu when more than one wagon is selected.
- `Config.WagonLocations`: `keepAtAssignedSpot`, `saveWorldPosition`, `transferCooldowns` (60 seconds, `0` is instant), `spawnMode` (`'auto'`, `'road'`, `'player'`), `callCooldownSeconds` (5), and `simulateNodes` for fixed spawn points.
- `Config.Interaction`: distances for the yard spots and for the rear, side, and wheel points on the wagon.
- `Config.WagonManagement`:
  - `reload`: recreates the active wagon and its horses, with a 10 second cooldown.
  - `sell`, `gift`, `sharing` (`maxPlayers = 10`), `craft`: each has `enabled`.
  - `rename`: `item`, `consume`, `maxLength`.
  - `transfer`: moving a wagon to another yard costs `price = 150` in `currency = 'money'`.
  - `storage`: `shared`, and `blockWhenDamaged` with `blockBelowHealth = 100` locks storage on a badly damaged wagon.
  - `repair`: `item`, `itemCount`, `consume`, and `priceAtSpot = 10`. At a yard, repair costs money and needs no item.
  - `wheels`: `item`, `itemCount`, `consume`, `durationSeconds`.
- `Config.WagonStatus`: `maxHealth = 1000`, and `undriveableBelow = 100` stops the wagon from being driven. `wheelDamage` has `chance = 60` per check, every `intervalSeconds = 10`, above `minimumSpeed = 12.0`.
- `Config.ComponentsEnabled`: turns `extras`, `tint`, `livery`, `lantern`, `propset`, and `horses` on or off in the equipment panel.
- `Config.WagonTint`: `'random'` or `'green'` for shop previews and for new wagons.
- `Config.ComponentGifting`: giving a component to a nearby player who owns a compatible wagon.
- `Config.Cargo`: `wood` and `coal` each name a wagon `model`, a `weightCapacity` (400), and the `items` with their `weight`. `hunting` names the hunting cart model. `animalsEnabled` switches animal storage.
- `Config.Ui`: `style` is `'classic'` or `'modern'`. `targetBars.dynamicBars` makes the status bars follow the wagon.
- `Config.Prompts.newPrompt` and `Config.KeyMapping`: the same options as in GUM Horses.

### Yards (`configs/configSpots.lua`)

`Config.Spots` is keyed by yard name. The default file has Valentine, Blackwater, Strawberry, Rhodes, Saint Denis, Annesburg, and Tumbleweed. Each yard has:

- `enabled`
- `townArea`: the area in which a dismissed wagon returns to this yard.
- `shopBlip`: `enabled`, `sprite`, `scale`, `name`.
- `jobPermissions`: `access` (empty allows everyone), `openClose`, and `fraction` (wagons stored here are shared with the jobs in `access`).
- `purchaseJobLock`: limits who can buy at this yard.
- `storeSetting`: `enabled`, `interaction`, `wagon` (the preview position).
- `mySetting`: `enabled`, `interaction`, `wagon`, `callSpawn`.
- `services`: `components`, `repair`, `storage`, `gift`, `transfer`, `craft`.
- `npcSetting`: `store` and `owned`, each with `enabled`, `model`, `heading`.

Keep the store and "my wagons" interaction points apart, at least twice `Config.Interaction.spotDistance`, so the two areas do not overlap.

### Wagon catalog (`configs/configWagons.lua`)

`Config.Wagons` has one entry per wagon model. Main keys:

- `enabled`, `name`, `category`
- `buyBlocked`: `true` makes the model crafting-only.
- `stables`: yard names where it is sold. Empty means every yard.
- `dollarPrice`, `goldPrice`, `sellPrice`
- `storage` (inventory capacity), `animalStorage` (carcass capacity), `bigWagon`
- `jobLock`: jobs allowed to buy. Empty allows everyone, and so does a list that contains `'unemployed'`.
- `groupLock`: framework groups allowed to buy.
- `purchaseJobLock` (optional): overrides `jobLock` for buying.
- `usageJobLock` (optional): restricts who can call the finished wagon.
- `identifierForBuy` (optional): see below.
- `crafting`: `enabled`, `price` (the starting fee), `jobLock`, and `ingredients` (`item`, `label`, `amount`).
- `wheelRepairIds`, `wheelRepairOffsets` (optional): wheel points for unusual models.

When `crafting.enabled` is `true`, a player pays `crafting.price` to start a construction project, calls the unfinished wagon at the yard, deposits the ingredients, and installs the wheels. An unfinished project takes one ownership slot.

### Restrict wagon purchases to specific accounts

Add `identifierForBuy` to a wagon entry in `configs/configWagons.lua`, next to `jobLock`:

```lua
identifierForBuy = {
    'steam:110000112345678',
},
```

- A missing, `nil`, `false`, or empty `{}` value lets everyone buy.
- Full `steam:...` values and raw hex IDs are both accepted. Always quote them as strings.
- It restricts buying a wagon and starting a new construction project. Job, group, and yard rules still apply.
- Wagons already owned, gifts, and player market sales are not affected.

### Components (`configs/configComponents.lua`)

`Config.Components` lists, per wagon model, the `extras`, `tints`, `liveries`, `propsets`, and other variants. Each variant has an `id` (do not change it), a `price`, and a `crafting` block:

- `crafting.enabled`: allows crafting this variant.
- `crafting.onlyCrafting`: `true` blocks buying and charges `price` as a crafting fee on top of the materials. `false` allows buying, or crafting for materials only.
- `crafting.ingredients`: `item`, `label`, `amount`.
- `crafting.jobs`: empty allows everyone.

`Config.DraftHorseComponents` lists the draft horse team presets, common to all wagons.

### Draft horses (`configs/configDraftHorses.lua`)

- `Config.DraftHorseHandling`: `enabled` switches the handling effect. The wagon uses the average of the attached horses. `acceleration` limits speed gain, `pull` scales sustained throttle, and `braking` sets how fast the wagon stops. Horses without a rating count as `baseline = 4` on the 1 to 9 scale.
- `Config.DraftHorseAppearance.modelInfo`: one entry per horse coat, with `enabled`, `name`, `category`, `label`, `price`, and `stats` (`pull`, `acceleration`, `braking`).

### Animal cargo (`configs/configCargo.lua`)

`Config.AnimalCargo` is the whitelist of animal and pelt models that hunting storage accepts. Each entry has a `name`, a `kind` (`animal` or `pelt`), and `big`. Add a model here to support another animal.

### Player wagon market (`configs/configPlayerWagonMarket.lua`)

`Config.PlayerWagonMarket` has `enabled`, `minPrice`, `maxPrice`, `maxDescriptionLength`, a `blip`, and a `markets` table. Each market key matches a `Config.Spots` key. Per market: `enabled`, `jobLock` (who can list and withdraw, empty allows any wagon owner), `interaction`, `displayWagon`, and `camera`. Anyone can browse and buy.

### Server settings (`configs/configServer.lua`)

- `ConfigSrv.integrationResources`: the server resources allowed to change wagon cargo, open wagon storage, or grant rentals through the exports below. Use the resource folder names, for example `['my_butcher'] = true`.
- `ConfigSrv.discordHook`, `ConfigSrv.webhookLink`, `ConfigSrv.webhookColor`: the Discord log. Put your own webhook in `webhookLink`, or set `discordHook = false`.
- `ConfigSrv.discordLogs`: `wagonPurchase`, `wagonCraft`, `wagonSale`, `wagonRename`, `wagonTransfer`, `wagonGift`, `wagonMarketListing`, `wagonMarketWithdrawal`, `wagonMarketPurchase`.

### Prompt keys and text (`configs/configLanguage.lua`)

All text is in `Config.Language.text`. Prompt labels and keys are in `Config.Language.promptKeys`, for example `call = {'Call wagon', 'E'}`. A key can be a key name or a control hash.

## Commands

GUM Wagons registers no chat commands. Players use the yard prompts and the calling key from `Config.WagonCalling`.

## Exports

### Client exports

```lua
exports.gum_wagons:openOwnedMenu()             -- the player's wagon list
exports.gum_wagons:openOwnedMenu('Valentine')  -- the same, at a yard
exports.gum_wagons:openWagonShop('Valentine')
exports.gum_wagons:openWagonNpcMenu('Valentine', 'owned') -- or 'store'
```

The menu exports return `true` when the menu opened and `false` when it could not (the yard is unavailable or access is denied). The yard name must match a key in `Config.Spots`.

| Export | What it does |
| --- | --- |
| `getWagon()` | Returns the active wagon entity and its database ID |
| `getWagonInfo(entity?)` | Returns `{id, entity, networkId, modelHash, record?}` or `nil`. Without an entity it uses the active wagon |
| `callWagon(wagonId, spotName?, callback)` | Calls a wagon the player owns or shares. Normal call limits and cooldown apply |
| `dismissWagon(wagonId?, callback)` | Dismisses a wagon. Without an ID it dismisses the active one |
| `performWagonAction(action, payload, callback)` | Runs a wagon action. The server still checks ownership and access |
| `storeCarriedAnimal(wagonId, callback)` | Loads the carcass or pelt the player is carrying |
| `takeWagonCargo(wagonId, uid, callback)` | Unloads one cargo entry |

Callbacks receive a result table with a `success` boolean and sometimes a `message`.

Actions for `performWagonAction`: `buy`, `craft`, `toggleSpot`, `call`, `dismiss`, `reload`, `select`, `deselect`, `storage`, `inspectStorage`, `repairWheelStart`, `components`, `craftComponents`, `loadCargo`, `unloadCargo`, `storeAnimal`, `takeAnimal`, `rename`, `transfer`, `sell`, `gift`, `share`, `revoke`, `repair`, `componentGiftTargets`, `giveComponent`.

```lua
exports.gum_wagons:performWagonAction('rename', { id = wagonId, name = 'New name' }, function(result)
    print('Rename succeeded:', result.success)
end)
```

### Server exports

Add `dependency 'gum_wagons'` to the manifest of the resource that calls these. Call them from a server thread, because they can yield. `sourceId` is the player's server ID. `wagonId` is the wagon's database ID, not an entity handle or network ID.

| Export | Returns |
| --- | --- |
| `getIntegrationVersion()` | `1` |
| `getWagon(wagonId)` | The wagon record, or `nil` |
| `getCalledWagons()` | The currently called wagons |
| `getPlayerWagons(sourceId)` | All wagons the player can access, or `nil, reason` |
| `getPlayerWagon(sourceId, wagonId?)` | One wagon, or `nil, reason`. Without an ID it finds the nearest accessible called wagon |
| `getWagonFromNetworkId(sourceId, networkId)` | The wagon, or `nil, reason` |
| `getWagonAccess(sourceId, wagonId)` | `true, 'owner'`, `'shared'`, or `'rental'`, or `false, reason` |
| `getHuntingCargo(sourceId, wagonId)` | The cargo list, or `nil, reason`. The player must be near the called wagon |
| `removeHuntingCargo(sourceId, wagonId, uids)` | `true, removedEntries`, or `false, reason` |
| `addHuntingCargo(sourceId, wagonId, entry)` | `true, savedEntry`, or `false, reason` |
| `openWagonInventory(sourceId, wagonId)` | `true`, or `false, reason` |
| `grantWagonRental(sourceId, wagonId, seconds)` | `true, expiresUnix`, or `false, reason` |
| `revokeWagonRental(wagonId)` | `true`, or `false, reason` |

The exports that change cargo, open storage, or handle rentals only work for resources listed in `ConfigSrv.integrationResources`. Other callers get `resourceDenied`.

Notes for integrations:

- `removeHuntingCargo` removes the whole requested set or nothing. Pay the player only after it returns `true`, and price the entries from your own server-side list.
- Hunting cargo entries have a `uid`, `model`, `kind` (`animal` or `pelt`), `name`, and `quality` (0 poor, 1 good, 2 perfect). Only models in `Config.AnimalCargo` are accepted.
- A rental gives a character temporary use of an existing wagon for 1 to 86400 seconds. It does not charge money or change ownership. The renter can call and dismiss the wagon, open storage, and handle hunting cargo. Rentals are cleared when `gum_wagons` restarts.

Error reasons you can get back: `resourceDenied`, `notReady`, `invalid`, `notFound`, `denied`, `tooFar`, `busy`, `unavailable`, `unsupported`, `cargoFull`, `cargoNotFound`, `saveFailed`, `damageStorage`.

```lua
-- Server side butcher example
local wagon = exports.gum_wagons:getPlayerWagon(sourceId)
if not wagon or not wagon.hunting then return end
local cargo = exports.gum_wagons:getHuntingCargo(sourceId, wagon.id)
if not cargo then return end
local uids = {}
for _, entry in ipairs(cargo) do uids[#uids + 1] = entry.uid end
local success, removed = exports.gum_wagons:removeHuntingCargo(sourceId, wagon.id, uids)
if not success then return end
-- Pay here, once, from removed[i].model and removed[i].quality.
```
