---
icon: leaf
---

# GUM Ecology

GUM Ecology is a wildlife and nature system for RedM. It handles plant spawning and gathering, animal skinning rewards, and a Field Atlas where players document the plants, animals, and wild horses they find.

## At a glance

- Video: [GUM Ecology on YouTube](https://youtu.be/RVELdYQU4OE)
- Store: [GUM Scripts RedM category on Tebex](https://gum-scripts.tebex.io/category/redm)

## What it does

### Plant spawning and gathering

- Plants spawn and respawn dynamically.
- You configure spawn areas, limits, and clusters, with rules for individual plants or areas.
- Time-based spawning: set the in-game hours when plants can appear.
- Weather conditions: restrict new spawns to specific weather.
- Terrain validation: control spawning by ground slope, elevation, water, and water depth.
- Spawn restrictions: keep plants from spawning too close to railways, other plants, or players.
- Spawn positions, respawn timers, and harvesting states persist across resource restarts.
- Harvesting rewards are customizable.

### Animal skinning and processing

- Loot is configured per animal species.
- Rewards can be randomized, with drop chances and minimum and maximum quantities.
- Native animal looting is supported.
- Rewards are validated on the server, with protection against duplicate processing rewards.
- Processed animals can optionally be removed.
- Processing an animal can count toward Field Atlas discoveries.

### Field Atlas

- Players discover plants, animals, and wild horses in a naturalist's journal.
- Each species can have up to five configurable discovery stages, and information unlocks stage by stage.
- The atlas tracks gathered materials and processed animals.
- It records discovery locations and conditions, including weather.
- Players can add personal notes.
- Progress is saved separately for each character.

### Observation and photography

- Players study animals with binoculars. Observation distance and duration are configurable.
- Players photograph plants, animals, and wild horses with the built-in camera.
- Photographs and exploration unlock species illustrations.
- Photography requirements are customizable, including an optional requirement that the animal is alive.

### Discovery requirements

- Discovery actions: study, gather, observe, photograph, and process.
- Requirements are configurable for each species and can be combined with ANY or ALL conditions.
- Optional wild horse taming progression.

### Other features

- Native or NUI interaction prompts.
- Configurable rendering and synchronization.
- Optional Discord activity logging.

## Before you install

Install **gumApi** first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).

- The resource depends on `gumApi` and `oxmysql`. Both must start before `gum_ecology`.
- GUM Horses integration is optional. It is used for the wild horse taming count in the atlas.
- If you are moving from the old version of GUM Ecology to the 2026-11-02 version, there is no converter. Open a ticket and send your files, and the developer can convert them for you.

## Install

1. Add the atlas item to your items. The default name is `Ecology_Book` (`Config.Atlas.item` in `configs/configAtlas.lua`). The script registers it as a usable item through gumApi. Using it opens the Field Atlas.
2. Make sure every reward item named in `configs/configHerbs.lua` and `configs/configAnimals.lua` exists in your items.
3. Start the resource after `gumApi` and `oxmysql`.

There is no SQL file to import. With `Config.Atlas.persistence.enabled = true`, the script creates two tables on start: `gum_ecology_atlas_plants` and `gum_ecology_atlas_animals`. The prefix comes from `Config.Atlas.persistence.table`. Each table holds one row per character and species.

Plant positions, remaining harvests, and respawn times are not stored in SQL. They are saved in the resource's KVP storage when `Config.Spawning.persistence = true`.

## Configuration

All config files are in the `configs/` folder and are not escrowed.

### `configs/configMain.lua`

- `Config.Debug` and `Config.DebugPrints`: test login, debug markers, and console output. Check that `Config.Debug` is `false` on a live server.
- `Config.adminRole`: the gumApi group allowed to use the admin commands. Default `'admin'`.
- `Config.clearCacheCommand` and `Config.resetAtlasCommand`: the admin command names. Set either to `false` to disable it.
- `Config.KeyMapping`: registers the prompt keys as key mappings, with `prefix` as the label prefix.
- `Config.Interaction`: `newPrompt` switches between NUI prompts (`true`) and native prompts (`false`). `useTarget` hands interaction to an external target system. The other keys are pickup radii and the server-side distance and cooldown checks.
- `Config.AnimalSettings`: `autoGather` rewards native animal looting. `removeAfterGather` deletes the animal after it is processed.
- `Config.Blips`: `activeHerbs` puts a blip on every generated plant position. `spawnAreas` shows the configured spawn areas. `sprite` is the blip sprite.

### `configs/configSpawning.lua`

`Config.Spawning` holds the global spawn rules:

- `railway`: keeps plants away from rails (`enabled`, `distance`, `height`).
- `minDistance`: minimum distance between active plants.
- `persistence`: saves positions, remaining harvests, and respawn times across restarts.
- `defaults`: the rules every plant uses unless it overrides them. Keys: `limit`, `maxTotal`, `respawnMinutes` (default `{20, 40}`, in real minutes), `cluster`, `avoidPlayerDistance`, `hours`, `weather`, and `terrain` (`maxSlope`, `maxHeightDifference`, `water`, `maxWaterDepth`).

To override the defaults for one plant, put a `spawn` table inside its `Config.Herbs` entry. It can also carry per-area overrides:

```lua
spawn = {limit = 8, maxTotal = 20, hours = {from = 18, to = 6},
    respawnMinutes = {15, 30}, cluster = {min = 2, max = 4, radius = 5},
    weather = {allow = {"RAIN", "DRIZZLE", "MISTY"}},
    areas = {[1] = {limit = 4}, [2] = {limit = 10, hours = false, weather = false}}}
```

`hours` also hides and shows existing plants. `weather` only controls new spawns.

### `configs/configHerbs.lua`

`Config.Herbs` is keyed by the prop model name. Each entry has:

- `coords`: the spawn areas, each as `{x, y, z, radius}`.
- `maxSpawn`: how many positions are generated per area (used when `spawn.limit` is `false`).
- `canGatherMore`: how many times one plant can be picked.
- `grounded`: lowers the prop into the ground.
- `items` and `randomized`: the rewards (see below).
- `blipSprite`, `blipName`, `animDict`, `animPart`, `animDur`.
- `spawn`: optional spawn overrides.
- `atlas`: the Field Atlas entry (see below).

### `configs/configAnimals.lua` and `configs/configHorses.lua`

`Config.Animals` and `Config.WildHorses` are keyed by `GetHashKey("model")`. Each entry has an `atlas` table, and animals also have `items` and `randomized`.

### Rewards

A reward row is `{"item_name", amount, "Label", chance}`:

```lua
items = {{"Gamey_Meat", 3, "Prime Meat", 100}, {"Fang", {1, 2}, "Animal Tooth", 50}}
```

- `amount` is a number, or `{min, max}` for a random amount.
- `chance` is a percentage. It defaults to 100 when left out.
- With `randomized = true`, the script picks one row from the list, then rolls its chance.

If the player cannot carry the reward, the harvest is not used up.

### Atlas entries

Every plant, animal, and wild horse entry has an `atlas` table:

- `enabled`: `false` hides the entry and disables its discovery. Gathering still works.
- `name`, `description`, `habitat`, `notes`, `observations`: the texts shown in the atlas.
- `illustrations`: which methods unlock the picture for this entry (`study`, `binoculars`, `camera`).
- `discovery`: `stage1` to `stage5`. Each stage has `requirements` (`inspect`, `binoculars`, `gather`, `photograph`, `tame`), `match` (`"any"` or `"all"`), and `reveal` (which texts the stage unlocks).
- `discovery.photographState` (animals and horses): `"alive"`, `"dead"`, or `"either"`.

Stages unlock in order. Requirement counts are cumulative totals per action, not extra counts per stage.

### `configs/configAtlas.lua`

- `Config.Atlas.enabled`: turns the atlas on or off.
- `revealAllForPreview`: shows every entry as complete without saving discoveries. Set it to `false` for normal play.
- `item` and `command`: how players open the atlas. Defaults are `"Ecology_Book"` and `"ecologybook"`. Set either to `false` to disable it.
- `illustrations`: the global picture rules, the built-in `camera` settings, and the binocular distance. The config notes that you should not run the gum_camera controls together with the built-in camera.
- `study`: plant study radius, and the binocular distance and duration for animals.
- `persistence`: `enabled` and the `table` prefix.
- `journal`: location history, weather recording, personal notes, and counters. `trackHorseTaming` counts successful GUM Horses taming.
- `ui`: the atlas title and subtitle.

### `configs/configPerformance.lua`

`Config.Performance` holds render distances, check intervals, sync limits, and debug markers. The plant render distance is `renderRadius` (default `150`, in metres).

### `configs/configServer.lua`

The Discord webhook is server-side only:

```lua
ConfigSrv.discordWebhook = {
    enable = true,
    url = "",
    color = 16755884,
    language = {
        playerGet = "Nature activity",
        plantHarvest = "Plant harvested",
        animalProcess = "Animal processed",
    },
}
```

### `configs/configLanguage.lua`

`Config.Language` holds every text, plus the control hash for each prompt (for example `takeHerb = {0x27D1C284, 'Take herb'}`).

## Commands

| Command | Who | What it does |
| --- | --- | --- |
| `/ecologybook` | Everyone | Opens the Field Atlas. Name set by `Config.Atlas.command`. |
| `/ecologyclearcache` | `Config.adminRole` or server console | Clears saved plant positions and the terrain cache, then starts a new generation. Name set by `Config.clearCacheCommand`. |
| `/ecologyreset [player ID]` | `Config.adminRole` or server console | Resets atlas progress for an online player. Without an ID it resets your own character. Name set by `Config.resetAtlasCommand`. |

## Events for connecting other scripts

You can hook another script, such as an XP or battle pass system, into GUM Ecology without editing it. Register the same event in an open server script of your own. When the event is triggered, your handler runs as well.

- `gum_ecology:animalAction` (server side): a player processed an animal. Arguments: `model, position, observation, reference`.
- `gum_ecology:herbAction` (server side): a player picked a plant. Arguments: `_, model, key, position, observation`.
- `gum_ecology:gatherAnimal` (client side): starts the skinning action on the animal the player is looking at. Use the server-side events for rewards.

Your handler receives the request as the client sent it. GUM Ecology's own distance, cooldown, and inventory checks run in its own handler, so do your own checks if the reward matters.

This example gives a different XP amount per animal:

```lua
local animals = {
    [GetHashKey('a_c_cedarwaxwing_01')] = 10,
    [GetHashKey('a_c_chicken_01')] = 15,
}
RegisterServerEvent('gum_ecology:animalAction', function(model)
    local xp = animals[tonumber(model)]
    if xp then
        -- call the export of your own XP or battle pass script here,
        -- with source and xp
    end
end)
```

The table can hold more than a number per animal, for example an XP amount and a category.

An older version of this page showed the handler as `function(configData, model)`. In the current code the first argument of `gum_ecology:animalAction` is the model.

## Customers also ran into these

### The Discord webhook settings are missing from the config

They moved. The old `Config.discordWebhook` is now `ConfigSrv.discordWebhook` in `configs/configServer.lua`.

### `Config.RenderRadius` does nothing

The key was replaced. Set `Config.Performance.renderRadius` in `configs/configPerformance.lua`.

### Herb blip settings are gone

`Config.blipSetting` was replaced by `Config.Blips` in `configs/configMain.lua`, with `activeHerbs` and `spawnAreas`.

### Processed animals are not removed

`AnimalGatherRemove` was replaced by `Config.AnimalSettings.removeAfterGather` in `configs/configMain.lua`. It is `false` by default.
