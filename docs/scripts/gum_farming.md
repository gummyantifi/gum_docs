---
icon: wheat-awn
---

# GUM Farming

GUM Farming is a configurable farming system for RedM. It covers everything from simple seed planting to farms with NPC workers, automation, and shared farm resources.

## At a glance

- Video: [GUM Farming](https://www.youtube.com/watch?v=D-Vquyp07LQ)
- Current version: 1.0.8 (2026-11-02). The rewritten farming left beta and became the official release with v1.0.7 on 2026-11-01.
- Resource folder: `gum_farms`. This page describes the rewritten version. The old `gum_farming` resource used different config files and tables.
- Frameworks: GUM support has confirmed it is compatible with RSG.

## What it does

### Farming stages and placement

- Turn land marking, soil preparation, and direct planting on or off.
- Let players plant anywhere, only on farms, or set restrictions per crop.
- Use optional grid planting with adjustable dimensions and spacing.
- Choose individual, grid-based, or simplified farming.
- Configure farming zones, jobs, permissions, and plant limits.

### Crop management

- Crops track growth, water, fertilizer, pests, condition, and quality.
- Each crop mechanic and interaction can be enabled or disabled.
- Players can water, fertilize, treat, or remove pests on one plant or on every plant within a configurable radius.
- Plants can use their own water and fertilizer, or draw from shared farm-wide water and fertilizer.
- Rain watering, scarecrows, watering cans, and refill pumps are optional.
- You can define custom crops, growth models, rewards, and regrowing trees.

### Harvesting

- Harvest a single plant or several crops within a radius.
- Configure harvest limits, rewards, animations, and durations.
- Crop quality and condition can influence harvest rewards.
- Harvested products can be converted back into seeds.

### Farm information

- Players can see statistics for an individual plant.
- Farms show farm-wide statistics and shared resource levels.
- Information can be shown in an NUI panel, as 3D text, or through prompts.
- You decide which statistics players can see.

### NPC workers

- Workers can water and fertilize automatically.
- Workers can remove pests and treat plants.
- Workers can harvest automatically.
- Each worker task can be enabled or disabled separately.
- Worker actions can target one plant or a radius.
- Work intervals and resource thresholds are configurable.
- Workers use supply stations and a storage for harvested products, or can carry supplies instead of using supply stations.

### Farming jobs and businesses

- Create job-restricted farms.
- Set permissions for individual farming actions.
- Allow selected actions for players without the required job.
- Give job-based work speed bonuses.
- Use shared farm resources and overall farm statistics.
- Configure worker behaviour for professional farms.

### Farming styles you can build

- Simple: direct planting, basic growth, and harvesting without preparation stages.
- Realistic: land marking, soil preparation, watering, fertilizing, pests, quality, and weather effects.
- Industrial: large grids, shared resources, workers, automated maintenance, and harvesting.
- Open world: planting outside dedicated farms with configurable crop restrictions.
- Hybrid: different mechanics for ordinary players and farming professions.

## What the script needs

- gumApi. It is declared as a dependency in `fxmanifest.lua`. Install it first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).
- An updated gumApi. With `Config.interaction.newPrompt = true` (the default), the script calls gumApi's new prompt exports. Version 1.0.5 and later need the updated gumApi, so update gumApi when you update farming.
- oxmysql. Farm data is read and written through `exports.oxmysql`.
- Optional: `gum_skills` (the `fastRake` and `moreItems` skill points and harvest experience under the `gum_farming` skill), `gum_houses` (house farm plots and their permissions), `gum_target` (targeting water pumps), and `gum_ranch` (filling ranch troughs from a filled bucket). Each one is only used when it is running.

## Install

1. Put the `gum_farms` folder in your resources and start it after gumApi and oxmysql. Keep the folder name `gum_farms`. GUM Ranch looks for a resource with that name.
2. You do not need to import SQL. On start, the script creates the table named in `Config.database.table` (default `gum_farms`) and a second table with the suffix `_helpers` (default `gum_farms_helpers`) for scarecrows, workers, and supply stations.
3. Add the items the config uses to your items:

| Used for | Config key | Stock item IDs |
| --- | --- | --- |
| Hoe | `Config.planting.hoeItem` | `hoe` |
| Seeds | `seedItem` of each crop in `Config.plants` | `Hop_Seed`, `Wild_Carrot_Seed`, `Tea_Leaves_Seed`, and one per crop |
| Harvest | `harvest.item` of each crop | `Hop`, `Wild_Carrot`, `Tea_Leaves`, and one per crop |
| Water | `Config.care.water.sources` | `Clean_Water_Bottle` |
| Fertilizer | `Config.care.fertilizer.sources` | `Fertilizer`, `Big_Fertilizer`, `Compost` |
| Water tools | `Config.waterTools.tools` | `Watering_Can`, `Teapot_Can` |
| Helpers | `Config.helpers.scarecrow.item`, `Config.helpers.worker.item`, `Config.helpers.station.item` | `scarecrow`, `Farm_Worker`, `Farm_Supply_Station` |

The script registers the hoe, every seed, the water tools, and the helper items as usable items. A harvest item is registered as usable when its crop has `herbToSeed.enabled = true` or a `herbToSeed.useEvent`.

### Upgrading from the old gum_farming

The version released on 2026-10-26 uses new config files and a new SQL table. The developer provides a converter (`GumConverter.exe`, posted in the GUM Scripts Discord as the "gum_convertor tool") for moving an old farm setup across:

1. Back up your original `gum_farming` resource and your database.
2. Place the original resource, including its configuration files, in `old scripts/gum_farming/` next to `GumConverter.exe`. Add SQL dumps of `farming`, `farmingdata`, and `farmingdatafree` if you use them.
3. Run the converter and select `gum_farming → gum_farm`.
4. Under **New scripts CFGs**, select the new resource's configurations. Keep automatic SQL detection enabled. Leave **Target SQL table** as `gum_farms` unless you changed `Config.database.table`.
5. Click **Convert configs + SQL**. The results appear in `converted/gum_farm/`.
6. Stop the old farming resource, start the new one, and check plants, farms, ownership, and helpers.

The converter does not modify your database automatically. Without an SQL dump it converts configurations only, and saved progress is not carried over.

The developer recommends starting from the full folder downloaded from the portal and replacing only the config files the converter produced.

Keys from the old config are not read by the new version. `growTime`, `updateTime`, `potStageProps`, `bucketItemId`, `wateringCanId`, `Config.PumpModel`, `disableHoe`, `Config.CanDestroyJobs`, and `Config.KeySetting` are gone. `weatherAffect` still exists, now under `growth` of each crop in `configs/configPlants.lua`. The current keys are described under "Config files" below.

## Config files

All config files are in the `configs` folder. Restart the resource after changing them.

### `configs/configMain.lua`

The `Config` table with the general settings:

- `Config.KeyMapping`: `enabled` and `prefix` (default `'GUM Farms'`).
- `Config.planting`: `mode` (`'farms'`, `'anywhere'`, or `'perPlant'`, default `'perPlant'`), `maxPlantsPerPlayer` for open-world planting (default `5`), `jobWorkSpeed` per job, `hoeItem`, `startPoint` (`'mark'`, `'prepare'`, or `'plant'`), `useSeed`, and `grid` (sizes, `spacing`, `attach`).
- `Config.interaction`: `newPrompt`, interaction distances, `minimumPlantSpacing`, the plant marker, the grid preview, and the land point visuals.
- `Config.features`: `interactions` turns each action on or off (`destroyLandPoint`, `plant`, `water`, `fertilize`, `procure`, `pestControl`, `harvest`, `destroy`). `statusDisplay` sets the status `mode` (`'3dText'`, `'prompt'`, or `'nui'`) and which fields are shown.
- `Config.ui.plantStatus`: the farm and plant status panels.
- `Config.care`: `procure`, `pestControl`, `water`, and `fertilizer`. Each has a `mode` (`'plant'` or `'radius'`, plus `'farm'` for water and fertilizer) and a `radius`. `water.sources` and `fertilizer.sources` list the items, how many are used, and how many points they add.
- `Config.waterTools`: carried water tools and pumps.
- `Config.weather`: rain watering (`enabled`, `waterPerMinute`, `affectFarmSupply`).
- `Config.status`: dead plant removal, `pests`, `procure` (condition), and `quality`.
- `Config.harvest`: `mode` (`'plant'` or `'radius'`), `radius`, `maxPlantsPerAction`.
- `Config.seedProcessing.maxItemsPerAction`: how many harvested items can be turned into seeds at once.
- `Config.helpers`: `scarecrow`, `worker`, and `station`.
- `Config.animations`: animations and durations per action.

Settings that tickets asked about most:

- Planting without a hoe: `Config.planting.useSeed.requireHoe = false` (the default) lets players plant from a seed without a hoe. `Config.planting.startPoint = 'plant'` skips marking and preparing soil.
- Water tools: each entry in `Config.waterTools.tools` sets the prop `model`, `capacity` (pours per fill), `amount` (water points), and `radius` (watering range in meters).

```lua
tools = {
    Watering_Can = {model = 'p_bucket03x', capacity = 2, amount = 100, radius = 4.0, handOffset = {0.0, 0.0, 0.0}},
    Teapot_Can = {model = 'p_wateringcan01x', capacity = 2, amount = 100, radius = 4.0, handOffset = {0.0, 0.12, 0.08}},
},
```

- Pumps: `Config.waterTools.pumpModels` lists the pump prop models (default `{'p_waterpump01x'}`). Leave `pumpLocations` empty to allow any matching pump.
- Dead plants: `destroyDeadPlants` and `destroyDeadTrees` are in `Config.status`.

```lua
status = {
    destroyDeadPlants = true, -- Remove non-tree plants when quality AND condition reach 0.
    destroyDeadTrees = false, -- Also remove tree plants when quality AND condition reach 0.
```

### `configs/configPlants.lua`

`Config.plants` holds one table per crop. The stock file has 43 crops. Each crop has:

- `label`, `statusHud`, `seedItem`, `allowedFarms` (`'all'` or a list of farm names).
- `planting.mode` (`'farms'`, `'anywhere'`, or `'both'`, used when the main mode is `'perPlant'`) and `planting.maxPlantsPerPlayer`.
- `initial`: starting `water`, `fertilizer`, `pests`, `procure`, `quality`.
- `growth`: `minutes` to mature, `waterUsePerMinute`, `fertilizerUsePerMinute`, and `weatherAffect`.
- `harvest`: `item`, `min`, `max`, the `animation`, and `bonuses` per stat.
- `isTree` and `tree` (`harvestsPerGrowth`, `fruitGrowMinutes`) for trees that regrow fruit.
- `herbToSeed`: `enabled`, `count` (seeds per harvested item), and an optional `useEvent`.
- `stages`: a list of `{growth, model, groundOffset}`.

To shorten growth, lower `growth.minutes` on the crop. To stop rain from watering one crop, set its `growth.weatherAffect = false`. To stop rain watering for every crop, set `Config.weather.enabled = false` in `configs/configMain.lua`.

```lua
growth = {
    minutes = 90,
    waterUsePerMinute = 1,
    fertilizerUsePerMinute = 0.35,
    weatherAffect = true,
},
```

### `configs/configFarms.lua`

- `Config.farms`: one entry per farm. A farm has `enabled`, `maxPlants`, `access`, and either a `polygon` (a list of `{x, y, z}` points) or a `center` with a `radius`. Optional keys are `label`, `clearZone`, `verticalTolerance`, `jobs`, `permissionsWithoutJob`, and a `worker` override.
- `access` can be `'free'`, `'shared'`, `'owner'`, or `'job'`. With `'job'`, the player's job must be in `jobs`. `permissionsWithoutJob` then allows single actions for everyone else, for example `{water = true, fertilize = true}`.
- `Config.farms._anywhere` holds the open-world settings and has no polygon.
- `Config.CleanZones`: extra vegetation cleanup spheres outside farm polygons.

Workers are for job farms only.

### `configs/configLanguage.lua`

`Config.Language` with the texts and, in `promptKeys`, the label and key of each prompt:

```lua
promptKeys = {
    markLand = {'Mark soil', 'MOUSE_LEFT'},
    gridSmaller = {'Grid size', 'MOUSE_WHEEL_DOWN'},
    gridLarger = {'Grid size', 'MOUSE_WHEEL_UP'},
    water = {'Water', 'F'},
    harvest = {'Harvest', 'E'},
```

The keys for water tools are in `Config.waterTools.keys` in `configs/configMain.lua` (`place`, `fill`, `take`, `pourFarm`).

### `configs/configPerformance.lua`

- `Config.performance`: render distances and refresh intervals.
- `Config.synchronization`: network batching.
- `Config.database`: `table` (default `'gum_farms'`), `updateIntervalSeconds` (growth update, default `60`), `saveIntervalSeconds` (default `900`), and the save batch settings.

### `configs/configDebug.lua`

`Config.Debug`: `Login`, `Client`, `Server`, `JS`, `DrawTexts`, `DrawTextRadius`. With `Client = true` and `Config.performance.debugPolygons.enabled = true`, farm boundaries are drawn in the world.

## Customers also ran into these

### Console error: attempt to call a nil value (field 'registerItem')

Cause: gumApi is not installed or did not start first, so farming cannot find the function it calls.

Fix: download and install gumApi, and start it before farming.

### After converting: attempt to index a nil value (field 'Config') and "could not find client_script `cfg/config.lua`"

The console also warns about `cfg/configHerbs.lua`.

Cause: the `fxmanifest.lua` in use is the old one. The old resource loads `cfg/config.lua` and `cfg/configHerbs.lua`. The new resource has no `cfg` folder. Its `fxmanifest.lua` loads six files from `configs/` (`configMain.lua`, `configPerformance.lua`, `configDebug.lua`, `configPlants.lua`, `configFarms.lua`, `configLanguage.lua`).

Fix: use the full `gum_farms` folder from the portal. Run the converter, then replace only the files in `configs/` with the converted ones. Do not copy the old `fxmanifest.lua` or the old `cfg` folder into it. Run a refresh and restart the resource.

### The config files in the beta download look encrypted

Cause: the default config files in the first beta upload were encrypted. They were reuploaded on 2026-10-27 so they can be edited.

Fix: download the files again from the portal and check the new configs. If you had already converted your old config, run the conversion again against the fresh files.
