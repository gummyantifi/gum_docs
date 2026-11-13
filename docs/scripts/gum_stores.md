# GUM Stores

GUM Stores is an NPC and player-owned store system for RedM. It includes dynamic pricing, weekly limits, opening hours, and support for faction businesses.

## At a glance

- Video: [GUM Stores](https://www.youtube.com/watch?v=vsaSYGjNYaY). The developer shows some of the store mechanics in it.

## What it does

### NPC stores

- An NPC can sell items, buy items, or do both.
- Every item has its own buy and sell price.
- Purchase and selling limits are configurable.
- Limits reset automatically on a weekday you choose.
- The UI and store layouts are customizable.
- Different store types are supported, including general stores and butchers.

### Player-owned stores

- Players or factions can create and manage their own businesses.
- You set which jobs are allowed to create stores.
- Stores are rented, with recurring payments.
- Owners can sell items to customers and buy items from players.
- Owners can edit product prices at any time, and add or remove products.
- Ownership can be shared with other players, which suits faction businesses.
- A configuration file controls exactly which items player-owned stores are allowed to buy, so you can balance your server's economy.

### Opening hours

- Each NPC store can have its own opening and closing times.
- The NPC appears when the store opens and disappears after closing.
- Trading is disabled while the store is closed.

### Dynamic tax on NPC stores

- As players buy products, the store's tax increases and product prices rise.
- Players can lower the tax, and with it the prices, by restocking stores with supply crates.
- The tax system can be disabled completely if you prefer static prices.
- Minimum and maximum tax values are configurable. Example values from the description: base tax 0.50, minimum tax 0.40, maximum tax 1.00.

### Butcher economy

- The butcher runs its own independent economy.
- The more players sell, the lower the butcher's buying prices become.
- Delivering export bags reduces stock and improves buying prices.
- Hunted animals are supported, and the items are configurable.

### What you can configure

Store inventories, buy and sell prices, the tax system, opening hours, the weekly reset day, player permissions, rental costs, UI appearance, and buyable items.

## Before you install

Install **gumApi** first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).

The reworked version of the stores was released on 2026-07-04 as a beta. At that time it was downloaded on the portal through the three dots menu by selecting BETA. It comes with new configuration files that include descriptions and some pre-set shops showing how everything works.

## Configuration notes

- `deleteCommand` in `Config.lua`, under `CreateCommand` (added 2026-08-02). It was added together with store deletion:

```lua
deleteCommand = "deleteStore",
```

- New translation lines in `ConfigLang` (added 2026-08-02), needed for store deletion:

```lua
notifyYouDoNotOwnThisStore = "You do not own this store.",
notifyNoStoreNearby = "There is no store nearby.",
doYouWantDeleteStore = {"Do you want to delete the store? <br>All items in the store will be lost.", "Yes", "No"},
deleteStore = "Delete store",
```

- `sellPrice` is case sensitive. Written as `sellprice`, the key is not read.

## Common problems and fixes

### Selling at some stores does not work after moving an old config to the new version

This was reported with stores that buy from players (general stores kept working) after converting a config from a much older version.

Cause: the sell price key was written as `sellprice`.

Fix: rename it to `sellPrice`, with a capital P, on every item.

### Store names show as "Undefined"

Cause: a bug introduced by the update before 2025-03-04.

Fix: update `client.lua` and `server.lua` to the 2025-03-04 version or newer.

### SQL error on start about the database tables

Cause: in the beta, the script could try to create its database tables again after they had already been created.

Fix: update to the 2026-07-05 beta files or newer.

### First purchase in a player-owned shop says the item does not exist

Cause: a bug in the beta where the first attempt failed and the second worked.

Fix: update to the 2026-07-05 beta files or newer.

### Player-owned store blips do not load, or the job check fails when creating a store

Cause: the script checked the player's job incorrectly when creating player-owned stores, and player-owned store blips were not loading correctly.

Fix: update client and server to the 2026-08-01 version or newer.

## Recent changes

- 2026-08-24: A store no longer opens for a player who does not have the job required to open it.
- 2026-08-12: When a player is buying items from other players, clicking the same item quantity again removes the buy offer. The interaction radius for opening shops is slightly larger.
- 2026-08-02: The job that created a store can now delete it, and the store management menu has a "Delete Store" button. Add `deleteCommand` to `Config.lua` under `CreateCommand` and the four new lines to `ConfigLang` (see Configuration notes).
- 2026-08-01: Fixed the job check when creating player-owned stores and fixed player-owned store blips not loading.
- 2026-07-05 (beta): Fixed the SQL error from re-creating existing tables, an occasional error when selling items, and the first-purchase "item does not exist" error in player-owned shops. Items you carry are now listed at the top of the sell list, and the sell quantity is capped at the amount in your inventory.
- 2026-07-04: New version released as a beta with a new UI and new, better organized configuration files. Player-owned shops can buy items back. When buying larger amounts you can hold the mouse button and the value changes faster.
- 2025-03-04: Fixed store names showing as "Undefined", optimized traders and player-run stores, and improved NPC spawning so nearby traders are no longer missing.
- 2025-03-01: Optimized the items query in `server.lua`.

*This page was built from the script's announcement, its changelog, and resolved support tickets. For anything not covered here, open a ticket in the GUM Scripts Discord.*
