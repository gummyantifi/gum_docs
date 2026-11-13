# GUM Pets

GUM Pets adds ownable pets to your RedM server: players buy them from pet shops, train them, breed them, and take them hunting. Pets have their own health, hunger, thirst, and age.

## At a glance

- Video: [GUM Pets, Updated](https://www.youtube.com/watch?v=cbx836pMOjk)
- A reworked version of the script was released for beta testing on 2025-05-13. Most changelog entries since then are for that beta version.

## What it does

### Shops and ownership

- Pet shops work in a similar way to the GUM stables shops.
- Wild animals, pets, and birds are supported.
- Pets can be gifted or traded to other players.
- You can enable or disable the system that keeps pets stored in a specific place (Valentine, for example).

### Training, bonding, and breeding

- Pets train bonding and develop skills.
- Pets can be bred with each other.
- You can set up a job (a pet trainer, for example) that makes training pets easier.
- Pets age over time.

### Out in the world

- Pets can hunt for the player, track down dead animals, and bring back small game.
- Players can throw a ball for their pet to fetch.
- Players can carry a pet in their hands or on their shoulder.
- Pets are adapted for running, horseback riding, and carts so they do not get in the way.

### Pet care

- Pets have their own metabolism. Players need to maintain their pet's health, hunger, and thirst.

## Before you install

Install **gumApi** first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).

- gumApi is the only dependency the developer names for GUM Pets.
- Your framework is selected in the gumApi config. Check that it is set correctly before starting GUM Pets.
- For player-owned stores, the script creates the `pets_store` database table itself (see "Player-owned pet stores do not work" below).

## Configuration notes

The beta release separated the configuration into several files. The changelog names these settings.

### Pet logic (`Config.PetsLogic` in `config.lua`)

- `ballItem`: the item used as the ball for fetch. Added 2025-06-03.

```lua
    ballItem = "Ball",
```

- `whistleKey`: the key used to whistle. Added 2026-02-28.

```lua
    whistleKey = 0x80F28E95, -- L key
```

### Per-pet settings (`configPets`)

- `canFetchBall`: set on every pet, controls whether that pet can fetch the ball. Added 2025-06-03.

```lua
    canFetchBall = false,
```

- `storeAnimation`: on 2025-06-04 the ability to adjust the Z-axis of the shoulder animation position was added, with this example. The shoulder animation is also taken from `storeAnimation`.

```lua
storeAnimation = {"creatures_bird@owl@normal@idle", "idle", 0.1}
```

- Heal items: added to `ConfigPets.lua` (and to `ConfigLangs.lua`) on 2025-05-22. Since 2026-04-06 the script correctly tells apart the `healItem` of each individual pet.
- `enabled`: a pet can be set to `enabled = false`.
- `whereTheyKept`: the option for where pets are kept. A bug that always stored purchased pets in the first shop slot while this was enabled was fixed on 2025-05-22.

### Jobs and shop access

- `Config.Jobs`: per-job settings. The keys seen in a customer config are `canTrain`, `canTrainSkill`, `canBreed`, `canHeal`, `canBuyCubs`, `expBoost`, `maxPets`, and `maxBreeds`. The default entry is keyed by `Config.DebugAndBaseOption.defaultUnemployedJob`.
- `canHeal`: if the healing injection does not work, check this value in the job settings.
- `jobForBuy`: set in the store configuration to restrict buying to listed jobs.

### Discord hook

- Discord hook support was added on 2026-08-18. The changed files were `fxmanifest` and `configSrv`.

### Language lines added by updates

If you keep your own language file, add these lines when you update.

Added 2025-06-03 (`configLang`):

```lua
    notifyYouNeedHaveCalledPet = {"You need to have called pet", 2000},
    notifyAfterNextCallPet = {"After next call your pet will be renamed", 2000},
    notifyBallIsLossed = {"Your ball is lossed, you need to buy new one", 2000},
    notifyYourPetCantFetchBall = {"Your pet can't fetch ball", 2000},
```

Added 2025-06-22:

```lua
doYouWantTransferPetToPlayer = {"Player ID", "Accept"},
promptPetTransfer = {"Transfer", 0x018C47CF},
```

Added 2025-08-15:

```lua
    inputPetsPrice = {"Accept", "Price"},
    inputPetsDesc = {"Accept", "Description"},
```

Added 2026-04-06:

```lua
notifyThisItemCantHealYourPet = {"This item can't heal this pet", 2000},
```

## Common problems and fixes

### The pet shop blip shows but I cannot interact, and the console shows "attempt to index a nil value"

Cause: gumApi is missing or did not start.

Fix: install gumApi and make sure it loads before GUM Pets. Check the server console to see whether gumApi reports a problem on launch.

### The healing injection does not work

Check `canHeal` in the job settings for the player's job.

### Player-owned pet stores do not work

Cause: the old `pets_store` table does not work with the current version.

Fix: update to the 2025-08-15 version or later, delete the `pets_store` table in the database, and let the script create a new one.

### The heal item is registered as a usable item once for every pet

Fixed on 2026-04-06. Pet revival items are now registered only once, and the correct `healItem` is distinguished for each pet. Update the client and server files.

### `jobForBuy` does not restrict who can use the shop

The `jobForBuy` function was fixed on 2025-06-22, and a job check that let a pet into the shop when it should not have was fixed on 2026-04-06. Update to the current version.

### Feeding a pet needs more items than configured

Example: 1 item was required but the player needed 2 in the inventory. Fixed on 2025-06-06 (server).

### A pet dies from bleeding after being treated

Fixed on 2024-12-05 (`client.lua`).

### A pet stays dead after being revived

Fixed on 2024-11-30, with further revival fixes on 2025-08-15 and 2026-04-06.

### Shop blips or shop NPCs do not show up

Shop blips were fixed on 2025-06-07. NPCs not being displayed was fixed on 2025-08-15.

### Players get an error when the first and only pet in a category is set to `enabled = false`

Fixed on 2026-08-16 (client).

## Recent changes

- 2026-09-07: Fixed saving a bred pet while the Discord hook is enabled (server).
- 2026-08-18: Added Discord hook support (`fxmanifest`, `configSrv`).
- 2026-08-16: Fixed the error shown when the first and only pet in a category is set to `enabled = false`.
- 2026-08-07: Fixed an issue when putting a pet into a player store.
- 2026-06-24: Fixed the "-" value shown for products when the minigame is disabled.
- 2026-04-06 (beta): Each pet's `healItem` is now distinguished correctly, revival items are registered only once, reviving no longer causes an error, and the shop job check was fixed. New language line `notifyThisItemCantHealYourPet`.
- 2026-02-28 (beta): Added `whistleKey` to `Config.PetsLogic`.
- 2025-09-30: Slightly reduced the size values for small pets.
- 2025-09-14: Fixed a job and group checking problem introduced by the previous update.
- 2025-09-11 (beta): The filter by group function now works.
- 2025-08-15 (beta): Fixed NPCs not displaying, breeding asking for fertility injections when both pets were already set to yes, pets duplicating on relog, and placing pets into a player-owned store. Delete the `pets_store` table so the script can recreate it. New language lines `inputPetsPrice` and `inputPetsDesc`.
- 2025-06-22 (beta): Added pet trading between players (aim at and target the pet). Fixed `jobForBuy`. New language lines `doYouWantTransferPetToPlayer` and `promptPetTransfer`.
- 2025-06-07 (beta): Fixed items not being removed for breeding repair, pet ball duplication, and missing shop blips.
- 2025-06-04 (beta): Added Z-axis adjustment for the shoulder animation through `storeAnimation`.
- 2025-06-03 (beta): Pets can fetch a ball. Hold the right mouse button and click the left button to throw (hold longer for a stronger throw). New settings `ballItem` and `canFetchBall`, four new language lines, and a fix for pet renaming.

*This page was built from the script's announcement, its changelog, and resolved support tickets. For anything not covered here, open a ticket in the GUM Scripts Discord.*
