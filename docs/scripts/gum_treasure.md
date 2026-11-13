---
icon: gem
---

# GUM Treasure

GUM Treasure is a treasure hunting script for RedM. Players follow maps to a spot, dig with a shovel, and collect rewards.

## At a glance

- Video: [Treasure hunting](https://youtu.be/isYgWI2NQ7w)
- Supported frameworks: RedEM, RedEM Reboot, VORP, GUM, QBR, RSG

## What it does

### Maps and items

- Add custom images for maps and items.
- Choose which maps a specific store offers.
- Generate preset maps at random, with options to generate all of them or reset them.
- Follow the included guide for creating new maps. It explains how to make custom maps in Photoshop that match the visual style you want.

### Searching and digging

- Once a player has searched a treasure, another player cannot search the same treasure again.
- Digging uses shovel animations for holding and digging.
- Searching has a configurable chance, and digging on the same coordinates is protected.

### Rewards and logging

- Rewards can be items, money, or gold, each with configurable chances.
- Discord webhooks are supported.

## Before you install

Install **gumApi** first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).

The script uses `oxmysql` for its database calls. Start it before `gum_treasure`.

## Install

1. There is no SQL file to import. On start the script creates the `treasure` table if it does not exist. It has three columns: `item` (the map ID), `searched` (0 or 1), and `updated`.
2. Add the shovel item to your items. Default `Shovel` (`Config.ShovelSetting.itemId`).
3. Add one item for every map in `Config.TreasureMaps`. The item name is the map's key. The shipped config has `Treasure_Map_1` to `Treasure_Map_30`.
4. Add every reward item named in the maps' `itemsReward`.
5. Give each map item an icon named `ITEMID.png` in your inventory image folder. The store loads the icon from the image folder that gumApi reports.
6. Start the resource after `gumApi` and `oxmysql`.

The script registers the shovel and every map as usable items. Using the shovel equips or puts away the shovel. Using a map opens its image.

## How it plays

1. The player walks up to a map store and buys a map. A store only lists maps that are active.
2. Using the map item shows the map image on screen.
3. The player uses the shovel item, then presses the "Dig" prompt. A dig takes `digTime` seconds.
4. If the player is inside the radius of an active map's `coords`, the server rolls `searchChance`. On success the treasure prop is dug up and the rewards are given.
5. A found treasure is marked as searched for everyone. With `searchNotify = true`, the next player who digs there is told that someone already found it.
6. A player cannot dig again within 2 metres of their last dig spot.

If a player digs at the spot of a map that is not active, the script removes that map from their inventory and tells them to buy a new one.

## Configuration

`config.lua` and `configSrv.lua` are in the resource root and are not escrowed.

### `Config.TreasureGenerator`

```lua
Config.TreasureGenerator = {
    resetAfterRestart = true,
    randomGenerator = true,
    maxTreasures = 5,
}
```

- `randomGenerator = false`: every map in `Config.TreasureMaps` gets a row in the `treasure` table and is active.
- `randomGenerator = true`: on start the script picks random maps until `maxTreasures` treasures are active.
- `resetAfterRestart = true`: on start, treasures marked as found are set back to not found in the `treasure` table.
- Both off: a found treasure stays found until you set its `searched` value back to `0` in the `treasure` table yourself.

### `Config.ShovelSetting`

- `itemId`: the shovel item. Default `"Shovel"`.
- `digTime`: seconds per dig. Default `15`.
- `searchNotify`: tells a player that the treasure at this spot was already found.

### `Config.Stores`

Each store has:

- `blipSprite` (`0` disables the blip) and `blipName`.
- `storeCoords`: where the "Open" prompt appears.
- `npcModel` (`false` disables the NPC) and `npcCoords` (`x, y, z, heading`).
- `canBuy`: the list of map IDs this store sells.

### `Config.TreasureMaps`

Each map is keyed by its item ID. The same ID is the name of its image file.

```lua
["Treasure_Map_1"] = {
    label = "Treasure Map 1",
    coords = {1246.32, -60.97, 100.15, 10.0}, -- x, y, z, radius
    price = 30,
    searchChance = 50,
    prop = "s_medcase_loot",
    itemsReward = {
        ["Gold_Dust"] = { label = "Gold dust", count = {1, 3}, chance = 50 },
    },
    moneyReward = { count = {0.2, 20.0}, chance = 50 },
    goldReward = { count = {1, 3}, chance = 0 },
},
```

- `label` and `price`: shown in the store.
- `coords`: the dig spot and its radius.
- `searchChance`: percent chance to find the treasure on each dig.
- `prop`: the model that is dug up.
- `itemsReward`: each item rolls its own `chance` and gives a random amount between the two `count` values. Items the player cannot carry are skipped.
- `moneyReward` and `goldReward`: one roll each, with a random amount between the two `count` values. Every map needs `itemsReward`, `moneyReward`, and `goldReward`. Set `chance = 0` to turn a money or gold reward off.

### `Config.Language`

All notifications, prompt labels, and the purchase question.

### `configSrv.lua`

The Discord webhook, server-side only:

- `ConfigSrv.discordHook`: `true` turns logging on. Default `false`.
- `ConfigSrv.webhookLink`: the webhook URL.
- `ConfigSrv.webhookColor` and `ConfigSrv.webhookLanguage`: the embed colour and titles.

The script logs map purchases and found treasures with their rewards.

## Adding a new map

1. Add an entry to `Config.TreasureMaps` with a new item ID.
2. Add an item with the same ID to your items, with an `ITEMID.png` icon.
3. Make the map image and save it as `html/img/ITEMID.png`. The steps for drawing it in Photoshop are in `readme/readme.md`.
4. Add the ID to the `canBuy` list of the stores that should sell it.
