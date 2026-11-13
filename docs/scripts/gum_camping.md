---
icon: campground
---

# GUM Camping

GUM Camping lets players build their own camp, fill it with props and storage, and keep it maintained. Camps can include a CraftMaster and a Butcher, and everything in a camp is saved in the database.

## At a glance

- Video: [GUM Camping](https://www.youtube.com/watch?v=awa_kQq7PXA)
- Frameworks: announced for VORP and RedEM. A 2024-10-23 update fixed camp prompts for RSG Core.
- A reworked version of the script was released as a test (beta) version on 2025-03-23. This page describes the reworked version.

## What it does

### Building a camp

- Players can freely choose where to place their camp, or you can require specific camp-building items, with different items representing different camp sizes.
- Camp size can be set by the building item used, scale with the number of camp members, or use a fixed radius.
- You can limit the maximum number of props and storage containers per camp.
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

- The script uses `oxmysql` for its database calls. Start it before `gum_camping`.
- If you are moving from the old version to the reworked version, remove the old SQL table and let the script create a new one.

## Install

1. There is no SQL file to import. On start the script creates the `camping` table if it does not exist. Each row is one camp, with its centre, owner, shared keys, props, condition, and the Butcher and CraftMaster contents.
2. Add the build items to your items. Every prop in `cfg/configProps.lua` has an `itemId` (for example `Campenter_1` for the flag and `Campfire_1` to `Campfire_4` for the campfires). The script registers each `itemId` as a usable item. Using the item removes one from the inventory and starts placement.
3. Add the items used by your Butcher and CraftMaster recipes, and the repair materials in `Config.destroySettings.materialForRepair`.
4. Start the resource after `gumApi` and `oxmysql`.
5. Set `debug = false` in `Config.baseSetting` before you go live.

## Configuration

All config files are in the `cfg/` folder and are not escrowed.

### `cfg/config.lua`

`Config.baseSetting`:

- `debug`: debug prints for setup. Turn it off on a live server.
- `renderRadius` (default `200`): distance at which camps and placed props are rendered. `tickForRender` is how often the script checks for nearby camps, in milliseconds.
- `onlyOneCampOwned`: `true` limits each character to one camp.
- `maxPropPerCamp` (default `50`, `0` is unlimited) and `maxStoragesPerCamp` (default `3`).
- `openClotheEvent`: the event that opens the player's wardrobe from a wardrobe prop. The first value is the event name. The second is `true` for a client event or `false` for a server event.

```lua
openClotheEvent = {"hobbs:openWardrobeHook", true},
```

- `commandManage`: the command that opens the camp management menu. Default `camp`.
- `campSizeRadius` (default `50`): the base camp radius.
- `radiusModifier.keyIncreaseRange`: extra radius by number of keys given out. The value is added to `campSizeRadius`. Set it to `nil` to disable it.
- `radiusModifier.needItemForBuild`: `nil` by default. When set, the player must hold one of the listed items to build.
- `blipSetting`: `everyOneSee` shows camp blips to all players. `blipSprite` is the sprite. `unknownOwner`, `sharedOwner`, and `owner` each hold a blip name and a blip colour modifier.

`Config.destroySettings`:

- `enableDestroy`: turns camp decay on.
- `daysToDecrease` (default `7`) and `damageToDestroy` (default `1`): a new camp starts at condition 100. On each resource start, a camp whose last decay is older than `daysToDecrease` days loses `damageToDestroy` condition. A camp whose condition is below 0 is deleted.
- `materialForRepair`: the items needed to repair, keyed by item name, each with a `name` (label) and a `count`. The repair cost is the missing condition multiplied by `count` for each item.

`Config.bannedTowns` lists the towns where building is blocked.

`Config.storageRobbery` is marked in the config as a feature still in work. Leave it disabled.

### `cfg/configProps.lua`

`Config.SpawnProps` is keyed by the prop model. Each prop has:

- `itemId` and `label`.
- `isFire`: `false`, or the model the prop switches to when the fire is turned on or off.
- `isEnter`: `true` gives the prop the camp management prompt.
- `isStorage`: `false`, or a number for the storage capacity.
- `isClothe`: `true` makes it a wardrobe.
- `isButcher` and `isCraftmaster`: `false`, or a table with the NPC model, offsets, and heading. A camp can have one of each.
- `buildTime` (seconds), `cleanGround`, `downward`.
- `rDistance`: a render distance for this prop. `nil` uses `renderRadius`.
- `oneTime`: a distance. The prop is placed outside the camp system as a temporary object and disappears when the player moves farther away than this.

`Config.MaxPeltInButcher` (default `20`) is the maximum number of entries the Butcher holds.

### `cfg/configButcher.lua`

- `Config.butcherProductEnable`, `Config.butcherStorageSystem`, `Config.butcherProductQue`: turn processing, the Butcher storage, and one-at-a-time processing on or off.
- `Config.butcherProducts`: keyed by animal model name. `timeForProduct` is counted in steps of about 10 seconds. Each entry in `productItems` is keyed by item name, with `label`, `checkLooted`, `getChance`, `minAmount`, and `maxAmount`.

### `cfg/configCraftmaster.lua`

- `Config.craftmasterProductEnable` and `Config.craftmasterProductQue`.
- `Config.craftmasterProducts`: a numbered list of recipes. Each has `category`, `timeForProduct`, `requestItems` (item name, `label`, `count`), and `productItems` (item name, `label`, `getChance`, `minAmount`, `maxAmount`).

### `cfg/configPelts.lua`

Lookup tables for the Butcher. It only accepts pelts listed in `Config.Pelts` and animals listed in `Config.Animals`. `Config.LegendaryTypes` maps legendary animal outfits.

### `cfg/configLang.lua`

`Config.Language` holds all notifications, inputs, prompts, and menu texts. Notifications are `{"text", seconds}`.

## Commands

| Command | What it does |
| --- | --- |
| `/camp` | Opens the camp management menu. You must stand inside a camp you own or hold a key for. The name is set by `commandManage`. |

## Customers also ran into these

### A camp disappeared

Camps do not despawn when their owner disconnects. Everything in a camp is saved in the `camping` table. Each camp has a condition value. With `Config.destroySettings.enableDestroy = true`, the condition drops over time and the camp is deleted when it runs out. Repair the camp from the camp menu before that happens.

### `Config.ClotheEvent` is not in the config

The key was replaced in the reworked version. Set `openClotheEvent` in `Config.baseSetting` to your clothing script's event, and set the second value to match whether it is a client or a server event.

### Language lines are missing after an update

If you keep your own `cfg/configLang.lua`, compare it with the shipped file after each update. Lines added by past updates include `youProcessed`, `youCantProcessLootedAnimal`, `inputSavePos`, `promptRotateMode`, `promptEditMode`, and `menuPropEdit`.

### The butcher does not process animals correctly

Butcher processing was rewritten in the reworked version. Update the client and server files, and make sure `youProcessed` and `youCantProcessLootedAnimal` exist in your `cfg/configLang.lua`.
