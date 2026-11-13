---
icon: campground
---

# GUM Camping

GUM Camping lets players build their own camp, fill it with props and storage, and keep it maintained. Camps can include a CraftMaster and a Butcher, and everything in a camp is saved in the database.

## At a glance

- Video: [GUM Camping](https://www.youtube.com/watch?v=awa_kQq7PXA)
- Frameworks: announced for VORP and RedEM. A 2024-10-23 update fixed camp prompts for RSG Core.
- A reworked version of the script was released as a test (beta) version on 2025-03-23. Most changelog entries since then are for that beta version.

## What it does

### Building a camp

- Players can freely choose where to place their camp, or you can require specific camp-building items, with different items representing different camp sizes.
- Camp size can be set by the building item used, scale with the number of camp members, or use a fixed radius.
- You can limit the maximum number of structures, storage containers, and other camp objects.
- Edit Mode lets players fine-tune the placement of camp props.

### Storage

- A camp can have multiple storage containers of different sizes.
- Storage containers can be renamed.

### CraftMaster

- The CraftMaster converts resources into other items, such as Logs into Wooden Planks.
- Recipes support configurable crafting times, random output amounts, and multiple outputs.
- Recipes are processed automatically through a crafting queue.

### Butcher

- The Butcher stores hunted animals and processes carcasses into materials such as Bones, Hides, and other resources.

### Durability

- Camps have a durability system. Players can be required to maintain and repair their camp.
- Abandoned camps can disappear automatically if left unattended for an extended period.

## Before you install

Install **gumApi** first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).

- If you are moving from the old version to the reworked (beta) version, remove the old SQL table and let the script create a new one.

## Configuration notes

The reworked version overhauled the configuration files to match the newer GUM scripts. The sources name these settings.

### Camp size and rendering

- `campSizeRadius`: the maximum allowed camp size radius.
- `radiusModifier`: increases the building radius in the camp based on the number of distributed keys.

```lua
    campSizeRadius = 20, -- Maximum allowed camp size radius
```

- A render radius can be set in the config, and the visualization distance for props can be adjusted for better performance.
- You can define a required item for camp construction. Depending on the item type, players build either a small or a large camp.

### Props

- All props are in a single configuration file, where each prop can have properties such as `isFire` (marks a campfire).
- `oneTime`: a placement option for a temporary prop that disappears once a certain distance is reached.

### Storage, blips, and damage

- You can define a different number of storage units per camp, assign different storage sizes by storage type, and name each storage.
- Blip customization settings are available.
- A damage multiplier is available, including date-based activation and deactivation options.

### Wardrobe

- `Config.ClotheEvent`: the event used to open a player's personal wardrobe. Added 2024-10-23.

```lua
Config.ClotheEvent = "gum_clothing:checkOwnedClothe"
```

### Language lines added by updates

If you keep your own language file, add these lines when you update.

Changed 2024-10-17 (config text for entry `[11]`):

```lua
text = "Fire manipulation",
```

Added 2025-09-22:

```lua
    youProcessed = {"You processed : ", 3},
    youCantProcessLootedAnimal = {"You can't process looted animal.", 3},
```

Added 2026-03-18 (`configLang`):

```lua
    inputSavePos = {"Do you want to save or cancel edit position?", "Save", "Cancel"},
    promptRotateMode = "Rotate Mode",
    promptEditMode = "Edit mode",
    menuPropEdit = "Edit",
```

## Common problems and fixes

### A camp disappeared

Camps do not despawn when their owner disconnects. Everything in a camp is saved in the database. Each camp has a condition value, and if the condition runs out and the player does not repair the camp, it is destroyed automatically. Repair the camp before its condition runs out.

### Camp prompts cannot be used on RSG Core

Fixed on 2024-10-23 (`client.lua`).

### Campfires stopped working after the optimization update

Fixed on 2024-12-05 (`client.lua`), along with other settings that prevented the use of items.

### `oneTime` props are visible only to the player who placed them

Fixed on 2025-06-22 (client).

### The butcher does not process animals correctly

A bug with starting animal processing was fixed on 2025-09-22. The butcher processing was then completely rewritten on 2025-09-25 and should work in almost all settings. Update the client and server files, and add the two language lines from 2025-09-22.

### The configured values for destroying a camp are not applied

Fixed on 2026-03-18 (client, server, and js files).

### A camp is deleted when a prop is picked up and it is not the last one

Fixed on 2025-03-25 (client and server).

## Recent changes

- 2026-11-01: Version v1.0.2. The server now checks that `damage` is a numeric value before processing it, and entries with no damage or negative damage are removed automatically. Replace `server.lua`.
- 2026-03-18 (beta): Added an edit mode to the management of placed items. Fixed the configured camp destroy values not being applied. Four new `configLang` lines.
- 2026-01-05 (beta): Added a short delay between the "near camp" notification and giving the item back.
- 2025-09-25 (beta): Butcher processing completely rewritten.
- 2025-09-22 (beta): Fixed a bug with starting animal processing. New notifications `youProcessed` and `youCantProcessLootedAnimal`.
- 2025-06-22 (beta): `oneTime` items are now visible to other players, not only the owner.
- 2025-05-13 (beta): Fixed occasional error messages and several server-side bugs, adjusted item recalculation during crafting, changed the condition for creating a campfire, and updated object ID generation to prevent ID merge issues.
- 2025-03-25 (beta): Fixed a camp being deleted when a prop that was not the last one was picked up. Fixed some blip settings.
- 2025-03-24 (beta): Fixed blip visibility when the setting for all players to see camp blips is disabled.
- 2025-03-23 (beta): Test version of the reworked script released. New menu and configuration files, camp size based on the number of keys, required build items for small or large camps, blip customization, a damage multiplier, multiple named storages per camp, a single props configuration file, a craftsman and a butcher, `oneTime` placement, and a prop visualization distance. Requires removing the old SQL table and letting the script create a new one.
- 2024-12-05: Fixed non-functional fires after the optimization update.
- 2024-10-23: Added `Config.ClotheEvent`. Fixed camp prompts on RSG Core.
- 2024-10-17: Config text for entry `[11]` changed to "Fire manipulation". Client-side prompt functions optimized to reduce the load on gumApi.

*This page was built from the script's announcement, its changelog, and resolved support tickets. For anything not covered here, open a ticket in the GUM Scripts Discord.*
