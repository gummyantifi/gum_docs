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

- GUM Horses integration is optional.
- If you are moving from the old version of GUM Ecology to the 2026-11-02 version, there is no converter. Open a ticket and send your files, and the developer can convert them for you.

## Configuration notes

### Discord webhook

Added 2025-02-25:

```lua
Config.discordWebhook = {
    enable = true,
    url = "",
    color = 16755884,
    language = {
        playerGet = "Player get : ",
    },
}
```

### Render radius

Added 2025-08-10. The changelog notes that this line needs to be added to your config:

```lua
Config.RenderRadius = 80
```

### Herb blips

Added 2025-10-06:

```lua
Config.blipSetting = {
    blipOnHerbs = true,--Every herb have blip
    blipsOnRadius = false--Radius on location where are herbs
}
```

### Removing processed animals

The option `AnimalGatherRemove` is named in the changelog. A problem with it was resolved in the client on 2025-03-10.

### Events for connecting other scripts

In a support ticket from March 2026 (before the 2026-11-02 update), the developer gave these events for hooking another script, such as an XP or battle pass system, into GUM Ecology:

- `gum_ecology:animalAction` (server side): fires after a player gathers an animal and the reward is called.
- `gum_ecology:herbAction` (server side): the same for herbs.
- `gum_ecology:gatherAnimal` (client side): the developer recommends the server-side event over this one, because the client-side event is connected to a slightly different system.

You do not need to edit GUM Ecology to use them. Register the event in any open server script of your own. When GUM Ecology triggers the event, your handler runs as well.

The developer's example gives a different XP amount per animal after skinning. `exports:bpXXXX` is a placeholder for the export of your own XP or battle pass script:

```lua
local animals = {
    [GetHashKey('a_c_cedarwaxwing_01')] = 10,
    [GetHashKey('a_c_chicken_01')] = 15,
}
RegisterServerEvent('gum_ecology:animalAction', function(configData, model)
    if animals[model] then
       -- animals[model] 
        exports:bpXXXX(source, 'hunting', animals[model])
    end
end)
```

Here `animals[model]` is the amount of XP the player receives for that animal. The developer also confirmed that the table can hold more than a number per animal, for example an XP amount and a category.

## Recent changes

- **2026-11-02:** Major update. Added the Field Atlas with discovery stages, binocular observation, the built-in camera, illustrations, and per-character progress. Reworked spawning with time-based spawning, terrain validation, spawn restrictions, weather conditions, per-plant and per-area rules, and spawn positions and respawn timers that persist across resource restarts. Spawn validation, terrain caching, and synchronization were optimized. There is no converter from the old version: open a ticket to have your files converted.
- **2026-06-03:** The script was adapted so herbs can be disabled. Client and server files changed.
- **2025-11-04:** A few fixes from the developer's own play testing.
- **2025-10-06:** Added `Config.blipSetting` for herb blips. Client files changed.
- **2025-09-22:** Herb generation on the map was rewritten, and herb handling was adjusted on both server and client.
- **2025-08-10:** Added `Config.RenderRadius`. Add the line to your config. `client.lua` changed.
- **2025-03-10:** Resolved the `AnimalGatherRemove` option. Client files changed.
- **2025-02-25:** Added the Discord webhook (`Config.discordWebhook`) and increased the pickup range for custom herbs. `client.lua` and `server.lua` changed.
- **2025-01-26:** Updated how information is managed between server and client, which should fix some issues with flowers.

*This page was built from the script's announcement, its changelog, and resolved support tickets. For anything not covered here, open a ticket in the GUM Scripts Discord.*
