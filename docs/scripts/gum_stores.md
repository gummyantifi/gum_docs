---
icon: store
---

# GUM Stores

GUM Stores is an NPC and player-owned store system for RedM. It includes dynamic pricing, weekly limits, opening hours, and support for faction businesses.

## At a glance

- Video: [GUM Stores](https://www.youtube.com/watch?v=vsaSYGjNYaY). The developer shows some of the store mechanics in it.

## What it does

### NPC stores

- An NPC can sell items, buy items, or do both.
- Every item has its own buy and sell price.
- Purchase and selling limits are configurable per item.
- Limits reset on the weekdays you choose.
- Different store types are supported, including general stores and butchers.
- A store can be limited to one job.

### Player-owned stores

- Players or factions can run their own businesses.
- You set which jobs the store creation command checks for.
- Stores are rented, with a weekly payment taken from a prepaid balance.
- Owners can sell items to customers and buy items from players.
- Owners can edit product prices at any time, and add or remove products.
- Access can be shared with other players, which suits faction businesses.
- A list in the config controls exactly which items player-owned stores are allowed to buy, so you can balance your server's economy.

### Opening hours

- Each NPC store can have its own opening and closing times.
- The NPC appears when the store opens and disappears after closing.
- Trading is disabled while the store is closed.

### Dynamic tax on NPC stores

- As players buy products, the store's tax increases and product prices rise.
- Players can lower the tax, and with it the prices, by restocking stores with supply crates.
- The tax system can be disabled per store if you prefer static prices.
- Minimum and maximum tax values are configurable per store.

### Butcher economy

- The butcher runs its own independent economy.
- The more players sell, the lower the butcher's buying prices become.
- Delivering export bags improves buying prices again.
- Hunted animals and pelts are supported, and the prices are configurable.

### What you can configure

Store inventories, buy and sell prices, the tax system, opening hours, limit reset days, the jobs for store creation, rental costs, and buyable items.

## Before you install

Install gumApi first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).

The script also calls `oxmysql` directly, so `oxmysql` must be running before it.

The reworked version of the stores was released on 2026-07-04 as a beta. At that time it was downloaded on the portal through the three dots menu by selecting BETA. It comes with new configuration files that include some pre-set shops showing how everything works.

## Install

1. Put the `gum_stores` folder in your resources and start it after gumApi and `oxmysql`.
2. Start the server. There is no SQL file to import. On start the script creates its two tables if they are missing: `stores` (tax and limit state of NPC stores) and `storesplayer` (player-owned stores).
3. Every item name used in `config.lua` must exist in your inventory items. The default config is filled with the developer's own item and job names, so go through it before you open the server.

## Configuration

| File | What it holds |
| --- | --- |
| `config.lua` | Jobs, player store settings, all NPC stores, animal and pelt prices |
| `configLang.lua` | All player-facing text, in `Config.Language` |
| `configDiscord.lua` | Discord logging, in `ConfigSrv` (server only) |

### Top-level settings in `config.lua`

| Key | Default | Meaning |
| --- | --- | --- |
| `Config.createJob` | list of job names | The jobs the create and delete commands check for. |
| `Config.Debug` | `true` | When on, the client asks the server for the player store list again shortly after the script starts. Useful when you restart the resource on a live server. |
| `Config.EnchantedTarget` | `false` | `true` switches store interaction from prompts to `gum_target` targets on the NPCs. |

### Player stores: `Config.playerStores`

| Key | Default | Meaning |
| --- | --- | --- |
| `createCommand` | `"createStore"` | Command that creates a player store. |
| `deleteCommand` | `"deleteStore"` | Command that deletes the nearest player store. |
| `storeCreatePrice` | `10.0` | Weekly rent of a store. |
| `maxPrepayWeeks` | `4` | How many weeks of rent an owner can prepay. |
| `buyOutItems` | long item list | The only items a player store is allowed to buy from players. |

If your config is older than store deletion, add `deleteCommand = "deleteStore"` to `Config.playerStores`, and add these four keys to `Config.Language` in `configLang.lua`: `notifyYouDoNotOwnThisStore`, `notifyNoStoreNearby`, `doYouWantDeleteStore`, and `deleteStore`.

Rent works like this: when a week has passed since the last payment, `storeCreatePrice` is taken from the store's prepaid balance. If the prepaid balance does not cover it, the store is deleted. Owners prepay in the store management menu.

### NPC stores: `Config.Stores`

Each entry is one store, keyed by a unique name. The key is also the row id in the `stores` table.

| Key | Meaning |
| --- | --- |
| `jobForStore` | `nil` for everyone, or a job name. A player without that job cannot open the store. |
| `isButcher` | `true` makes it a butcher: the prompt reads Butcher and players can sell a carried animal or pelt. |
| `blipSetting` | `BlipEnabled`, `BlipName`, `BlipSprite`. |
| `enableTax` | `{}` for static prices, or the tax settings below. |
| `npcSetting` | `Model`, `Coords` (`{x, y, z, heading}`), `Interaction` (`{x, y, z}` where the prompt shows), `OpenTime`, `CloseTime`. |
| `itemSetting` | The items, keyed by item name. |

The store is open from the `OpenTime` hour through the `CloseTime` hour (in-game clock). Leave `OpenTime` out to keep it open all day.

### Items: `itemSetting`

```lua
["Bread"] = { buyPrice = 0.10, buyLimit = 10, buyLimitReset = {1,3,5}, sellPrice = 0.11, sellLimit = 20, sellLimitReset = {2,4,6}, tax = 0.01 },
```

| Key | Meaning |
| --- | --- |
| `buyPrice` | Price the player pays. Leave it out if the store does not sell the item. |
| `sellPrice` | Price the player receives. Leave it out if the store does not buy the item. The default config also uses `0` for that. |
| `buyLimit`, `sellLimit` | Stock limit shared by all players, or `false` for no limit. |
| `buyLimitReset`, `sellLimitReset` | Weekdays on which the limit resets. `0` is Sunday, `6` is Saturday. `{}` means it never resets. The reset runs when the script starts on one of those days. |
| `tax` | How much one traded unit moves the store's tax, in percent points. |

Key names are case sensitive. `sellPrice` written as `sellprice` is not read.

### Dynamic tax: `enableTax`

```lua
enableTax = {
    type = "import",
    minToMax = {50.0, 400.0},
    importExportSetting = { ... boxPriceAndTax = {0.6, 4.0} }
},
```

The tax is a percentage. `100` means the prices from the config. Buy prices are multiplied by the tax and sell prices are divided by it, so a tax of `150` makes buying 50% more expensive and lowers what the store pays.

- `minToMax` is the lowest and highest tax the store can reach. A store without `minToMax` has static prices.
- Every unit bought or sold adds that item's `tax` value to the store's tax.
- `animalTax` (butchers) is added per dollar of animal value sold.
- `type = "import"` (general stores): a player starts a supply run from the store menu and travels to `startExportCoords`. Crates spawn at `boxCoords` and a wagon at `vehicleCoords`. The player loads the crates and drives them to `exportCoords`.
- `type = "export"` (butchers): the bags and wagon spawn at the store and are delivered to `exportCoords`.
- `boxPriceAndTax = {pay, tax}`: each delivered box pays the player `pay` (scaled by the current tax) and lowers the tax by `tax`.
- `boxGrid` sets the size of the box pile and `boxCoords` where it spawns.

### Animals and pelts

`Config.Animals` maps an animal model to three prices, for one, two, and three star quality. `Config.Pelts` maps pelt models to prices. A butcher pays these when a player sells what they are carrying.

### Discord logging: `configDiscord.lua`

`ConfigSrv.DiscordLog` turns logging on or off, `ConfigSrv.WebhookLink` is your webhook URL, and `ConfigSrv.Color` is the embed colour. `ConfigSrv.Lang` holds the log texts. Replace the webhook link with your own before you start the script.

## Commands

| Command | What it does |
| --- | --- |
| `/createStore <player id>` | Creates a player store at the position of the given player and makes that player the owner. The name comes from `Config.playerStores.createCommand`. |
| `/deleteStore` | Deletes the player store within 2 metres of you. The name comes from `Config.playerStores.deleteCommand`. |

Both commands check the caller's job against `Config.createJob`. A store owner can also delete their own store with the Delete store button in the store management menu.

## Customers also ran into these

### Selling at some stores does not work after moving an old config to the new version

The sell price key was written as `sellprice`. Rename it to `sellPrice`, with a capital P, on every item.

### SQL error on start about the database tables

The beta could try to create its tables again after they already existed. Current files create them only when they are missing. Update to the 2026-07-05 beta files or newer.

### First purchase in a player-owned shop says the item does not exist

A bug in the first beta, where the first attempt failed and the second worked. Update to the 2026-07-05 beta files or newer.

### Player-owned store blips do not load, or the job check fails when creating a store

Fixed in an update. Update client and server to the 2026-08-01 version or newer.
