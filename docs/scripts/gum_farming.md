---
icon: wheat-awn
---

# GUM Farming

GUM Farming is a configurable farming system for RedM. It covers everything from simple seed planting to farms with NPC workers, automation, and shared farm resources.

## At a glance

- Video: [GUM Farming](https://www.youtube.com/watch?v=D-Vquyp07LQ)
- Current version: 1.0.8 (2026-11-02). The rewritten farming left beta and became the official release with v1.0.7 on 2026-11-01.
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

## Before you install

Install **gumApi** first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).

- Version 1.0.5 and later need an updated gumApi. Update gumApi when you update farming.
- Filling the water cart needs GUM Stables connected to farming.

### Upgrading from the old gum_farming

The version released on 2026-10-26 uses new config files and a new SQL table. The developer provides a converter (`GumConverter.exe`, posted in the GUM Scripts Discord as the "gum_convertor tool") for moving an old farm setup across:

1. Back up your original `gum_farming` resource and your database.
2. Place the original resource, including its configuration files, in `old scripts/gum_farming/` next to `GumConverter.exe`. Add SQL dumps of `farming`, `farmingdata`, and `farmingdatafree` if you use them.
3. Run the converter and select `gum_farming → gum_farm`.
4. Under **New scripts CFGs**, select the new resource's configurations. Keep automatic SQL detection enabled. Leave **Target SQL table** as `gum_farms` unless your new resource uses another name.
5. Click **Convert configs + SQL**. The results appear in `converted/gum_farm/`.
6. Stop the old farming resource, start the new one, and check plants, farms, ownership, and helpers.

The converter does not modify your database automatically. Without an SQL dump it converts configurations only, and saved progress is not carried over.

The developer recommends starting from the full folder downloaded from the portal and replacing only the config files the converter produced. The script's readme also describes the conversion.

## Configuration notes

### Version 1.0.x

- `destroyDeadPlants` and `destroyDeadTrees` in `configs/configMain.lua` (added 2026-10-30, v1.0.5):

```lua
        destroyDeadPlants = true, -- Remove non-tree plants when quality AND condition reach 0.
        destroyDeadTrees = false, -- Also remove tree plants when quality AND condition reach 0.
```

- New translation lines for the grid size prompts (added 2026-10-30, v1.0.5). In `configs/configLanguage.lua`:

```lua
        gridSmaller = {'Grid size', 'MOUSE_WHEEL_DOWN'},
        gridLarger = {'Grid size', 'MOUSE_WHEEL_UP'},
```

In `configs/configLanguageCZ.lua`:

```lua
        gridSmaller = {'Velikost mřížky', 'MOUSE_WHEEL_DOWN'},
        gridLarger = {'Velikost mřížky', 'MOUSE_WHEEL_UP'},
```

### Settings announced before the 2026-10-26 rewrite

These were announced for the config of the version before 2026-10-26. The new version has its own config files, so confirm a key exists in your config before relying on it.

**Growth**

- `growTime` sets how long a crop takes to grow, and `updateTime` determines how often growth ticks. To shorten growth, lower `growTime` on the crop. The developer's example:

```lua
potStageProps = {
    [1] = {"s_inv_milkweed01bx", 51, 0.0}, 
    [2] = {"s_inv_milkweed01cx", 40, 0.0}, 
    [3] = {"s_inv_milkweed01x", 30, 0.0}, 
    [4] = {"s_milkweed01x", 1, 0.0}, 
}, 
weatherAffect = true,
growTime = 50,
```

- `weatherAffect`, placed under `locationTempAffect` (added 2025-08-10). It lets you turn off watering by rainy weather:

```lua
weatherAffect = true, -- If true, weather affects growth (watering during rainy weather)
```

**Watering**

- `bucketItemId` and `wateringCanId` gained a fourth value, the splash radius (added 2025-12-01). Watering then waters the plants within that radius:

```lua
bucketItemId = {"Watering_Can", 4, 100, 1}, -- Item | Capacity | How much water per herb | Splash radius
wateringCanId = {"Teapot_Can", 16, 100, 3}, -- Item | Capacity | How much water per herb | Splash radius
```

- `Config.PumpModel = "p_waterpump01x",` (added 2025-08-04).

**Planting and destroying**

- `disableHoe = true` in `Config.plantSetting` (added 2024-11-30) allows planting without needing a hoe.
- `Config.CanDestroyJobs` (added 2024-11-11) lists the jobs that are allowed to destroy plants:

```lua
Config.CanDestroyJobs = {
    ["Sheriff_VL"] = true,
}
```

**Rewards**

- A single plant can give multiple reward types (added 2026-04-04). Existing single-reward setups keep working. Example:

```lua
            [80] = {
                {"Hop", 5},
                {"Hop_Leaves", 5}
            },
```

**Keys**

- `Config.KeySetting` (added 2026-06-23) sets the key for each farming action:

```lua
Config.KeySetting = {
    cancelLand = 0x156F7119,
    markLand = 0x07B8BEAF,
    prepareLand = 0x2CD5343E,
    harvest = 0x2CD5343E,
    fertilize = 0x05CA7C52,
    procure = 0x20190AB4,
    water = 0xC97792B7,
    jerks = 0x6319DB71,
    destroy = 0x156F7119,
    takeWater = 0x07B8BEAF,
    pickScare = 0x07B8BEAF,
    fillBucket = 0x07B8BEAF,
    placeBucket = 0x07B8BEAF,
    takeBucket = 0x07B8BEAF,
    takeWaterFromCart = 0x07B8BEAF,
    giveWaterToCart = 0x2CD5343E,
}
```

## Common problems and fixes

### Console error: attempt to call a nil value (field 'registerItem')

Cause: gumApi is not installed, so farming cannot find the function it calls.

Fix: download and install gumApi, and start it before farming.

### After converting: attempt to index a nil value (field 'Config') and "could not find client_script `cfg/config.lua`"

The console also warns about `cfg/configHerbs.lua` and `cfg/configSrv.lua`.

Cause: the resource is not seeing the new config files. The warnings show the `fxmanifest.lua` in use referencing `cfg/` files that are not in the resource.

Fix: use the full folder from the portal and keep the resource name it has there. Run the converter, then replace only the config files with the converted ones. Run a refresh and restart the resource so the server picks up the new files.

### The config files in the beta download look encrypted

Cause: the default config files in the first beta upload were encrypted. They were reuploaded on 2026-10-27 so they can be edited.

Fix: download the files again from the portal and check the new configs. If you had already converted your old config, run the conversion again against the fresh files.

### The cart has no option to fill with water

Cause: filling the water cart depends on GUM Stables.

Fix: connect GUM Stables to farming.

## Recent changes

- 2026-11-02 (v1.0.8): Minor adjustments to the game loading process. Replace `fxmanifest.lua` and `server/loginGate.lua`.
- 2026-11-01 (v1.0.7): Farming moved from beta to official release.
- 2026-10-30 (v1.0.5): Requires a gumApi update. Adds key mapping for prompts, circular grid shapes with improved marker handling, and separate handling for destroying tree and non-tree plants. Add `destroyDeadPlants` and `destroyDeadTrees` to `configs/configMain.lua`, and `gridSmaller` and `gridLarger` to `configs/configLanguage.lua` and `configs/configLanguageCZ.lua`. Replace `fxmanifest.lua`, `server/main.lua`, `client/prompts.lua`, `client/promptKeymapping.lua`, and `client/main.lua`.
- 2026-10-27 (beta): Config files reuploaded so they can be accessed.
- 2026-10-26: New version released as a beta on the portal, with the grid system, optional farming stages, radius-based actions, shared farm resources, workers that harvest, and farm statistics. Existing setups need the converter (see "Upgrading from the old gum_farming").
- 2026-07-04: Fixed the change between stages 1, 2, 3, and 4.
- 2026-06-23: Added `Config.KeySetting` for configuring keys.
- 2026-04-04: One plant can give multiple reward types. Existing setups keep working.
- 2026-01-04: Fixed the watering can not being able to water a herb.
- 2025-12-04: A seed can no longer be used while you are preparing the soil for a seed.
- 2025-12-01: Farms no longer flicker during updates (a flicker now happens only when a plant is removed or collected), sync from server to client improved, and a splash radius was added to `bucketItemId` and `wateringCanId`.
- 2025-08-10: Added `weatherAffect` under `locationTempAffect` to turn off watering by rain.
- 2025-08-04: Added `Config.PumpModel`. Fixed an issue with deleting a farm.
- 2025-06-05: Fixed a herb being pickable twice.
- 2025-05-13: Adjusted loading of work farms and their limits, optimized farm loading, changed how new farms are created, and fixed the farmer NPC not being removed when the whole farm is collected.

*This page was built from the script's announcement, its changelog, and resolved support tickets. For anything not covered here, open a ticket in the GUM Scripts Discord.*
