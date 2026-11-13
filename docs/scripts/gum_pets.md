---
icon: dog
---

# GUM Pets

GUM Pets adds ownable pets to your RedM server: players buy them from pet shops, train them, breed them, and take them hunting. Pets have their own health, hunger, thirst, and age.

## At a glance

- Video: [GUM Pets, Updated](https://www.youtube.com/watch?v=cbx836pMOjk)
- A reworked version of the script was released for beta testing on 2025-05-13.
- Resource name: `gum_pets`

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

- gumApi is the only dependency the developer names for GUM Pets. The script also runs its database queries through `oxmysql`, so `oxmysql` must be started too.
- `fxmanifest.lua` does not declare either dependency, so the start order is up to you: start `oxmysql` and `gumApi` before `gum_pets`.
- Your framework is selected in the gumApi config. Check that it is set correctly before starting GUM Pets.

## Install

1. Put the `gum_pets` folder in your resources and start it after `oxmysql` and `gumApi`.
2. Do not import any SQL. The script creates its tables on start.
3. Add the items listed below to your inventory.
4. Edit the files in `cfg/`, then restart the resource.

### Database tables

The script creates these tables if they do not exist:

| Table | Holds |
| --- | --- |
| `pets` | Owned pets |
| `pets_store` | Pets listed in player-owned stores |
| `pets_breeding` | Active breeding records |
| `real_logic_pets` | The aging timer |

### Items

Item names are case sensitive. These are the defaults.

| Item | Used for | Where it is set |
| --- | --- | --- |
| `Ball` | Fetch | `Config.PetsLogic.ballItem` |
| `HorseTag` | Renaming a pet | `Config.PetsLogic.renameItem` |
| `Hay` | Resetting a pet's age | `Config.PetsLogic.ageingSetup.resetItem` |
| `BreederFix` | Fertility item for breeding | `Config.PetsStoreLogic.mating.giveMatingToMale` and `giveMatingToFemale` |
| `Health_For_Dog`, `Heal_Granuls` | Healing and reviving | `healItems` of each pet in `cfg/configPets.lua` |
| `Meat`, `Pork_Meat`, `Gamey_Meat`, `Granuls`, `Bison_Meat` | Food (Husky example) | `eatSettings.setupItems` of each pet |
| `Clean_Water_Bottle` | Water | `drinkSettings.setupItems` of each pet |

The ball, rename, age reset, and heal items are registered as usable items. Food and water items differ per pet, so check `setupItems` for every pet you sell.

## Configuration

All config files are in `cfg/`.

| File | What it holds |
| --- | --- |
| `config.lua` | `Config.DebugAndBaseOption`, `Config.PetsStoreLogic`, `Config.PetsLogic`, `Config.canHoldAnimals`, `Config.canHuntAnimals` |
| `configCoords.lua` | `Config.Shops` (pet shops) and `Config.PlayerStore` (player-owned stores) |
| `configJobs.lua` | `Config.Jobs` |
| `configPets.lua` | `Config.iconSkills` and `Config.PetsConfig` (one entry per pet model) |
| `configPetsStore.lua` | `Config.Descriptions` and `Config.PetsStore` (what the shops sell) |
| `configLang.lua` | `Config.Language` |
| `configSrv.lua` | Server only: Discord webhook |

### Base options (`Config.DebugAndBaseOption` in `cfg/config.lua`)

- `debug`: prints debug lines to the console. It is `true` in the default file. Set it to `false` on a live server.
- `defaultUnemployedJob`: the job name used as the default entry in `Config.Jobs`. Default `"unemployed"`.

### Shop logic (`Config.PetsStoreLogic` in `cfg/config.lua`)

- `callFromStore`, `sellOldForMoney`, `saveCallPet` (a called pet is called again after a relog).
- `healPrice`: `calculate` and `price`.
- `whereTheyAreKept`: `enabled = true` keeps each pet at the shop where it is stored. `transferPrice = 100` is the cost to move it to another shop.
- `mating`: `priceForPay = 100`, `time = 5760`, `chanceAfterBuy`, `chanceAfterMating`, the two fertility items (`nil` disables them), and `txAdminMatingCalculator` with `txAdminDisabledTimer`.
- `storePetAge`: age range of shop pets, `smallPets = {1, 5}` and `normalPets = {6, 10}`.

### Pet logic (`Config.PetsLogic` in `cfg/config.lua`)

- `ballItem`: the item used as the ball for fetch.

```lua
    ballItem = "Ball",
```

- `renameItem`: the item that renames the called pet.
- `cooldownForWhistle`: seconds between whistles.
- `whistleKey`: the key used to whistle.

```lua
    whistleKey = 0x80F28E95, -- L key
```

- `ageingSetup`: `ageUpdateTime`, `ageUpdate`, `maxAge = 20`, `deadOnOldTimer` (`0` disables death from old age), `deadOnOldChance`, `resetItem`, `resetAgeTo`.
- `statusSetup.metabolism`: `enabled`, `updateTime`, `healthDown`, hunger and thirst drain per movement state, and `gainWhileFeed`.
- `experienceSystem`: `petLevels` (XP needed per level) and `experienceGain`.
- `bondingSystem`: `enabled`, `chanceGainLevel` (first value for players, second for trainers), and the "bad situation" settings.

### Pet shops (`Config.Shops` in `cfg/configCoords.lua`)

Each numbered shop has a `locationName`, `blipSetting` (`blipEnabled`, `blipName`, `blipSprite`), `cameraSetting`, `spawnSetting` (`bigPet`, `smallPet`), and `npcSetting` (`enabled`, `model`, `coords`, `interaction`, `useTime`, `timeOpen`, `timeClose`). Do not remove the empty `[0]` entry.

`Config.PlayerStore` defines player-owned stores, each with `npcSetting`, `blipSetting`, and `baseSettings` (`maxPets`, `storeCoords`, `petCoords`).

### What the shops sell (`Config.PetsStore` in `cfg/configPetsStore.lua`)

Pets are grouped by category, then by color name. Each entry has:

- `enabled`: `false` removes the pet from the shops.
- `model`, `skinColor`
- `storePos`: the shop numbers from `Config.Shops` that sell it. `0` means all shops.
- `dollarPrice`, `goldPrice`, `sellPrice`
- `jobForBuy`: jobs that can buy it. A list that contains `"unemployed"` lets everyone buy.
- `groupForBuy`: groups that can buy it. `{"none"}` disables the group check.

The three Husky entries in the default file use `jobForBuy = {"Testjob"}`. Change that before you go live.

### Per-pet settings (`Config.PetsConfig` in `cfg/configPets.lua`)

One entry per pet model:

- `canFetchBall`: whether the pet can fetch the ball.

```lua
        canFetchBall = true, -- If pet can fetch ball
```

- `canTakeToHand`, `canPutOnShoulder`: a pet uses one or the other.
- `canHuntAnimals`, `canFindDeadAnimals`, `behaviorWild`, `canCallToMe`
- `storeAnimation`: the idle animation in the shop. It is also used for the shoulder position, and an optional third value adjusts the height on the shoulder:

```lua
storeAnimation = {"creatures_bird@owl@normal@idle", "idle", 0.1}
```

- `canBreedWith`: the models this pet can breed with.
- `eatSettings` and `drinkSettings`: animations and `setupItems`, in the form `["Label"] = {"Item_Id", count, {hunger, thirst}}`.
- `healItems`: `["Label"] = {"Item_Id", revive, healAmount}`. The second value sets whether the item also revives a dead pet. A heal item only works on pets that list it.
- `skillList`: each skill has `skillName`, `skillIcon`, `unlockAtLevel`, `learnChance`, and its animations.

### Jobs (`Config.Jobs` in `cfg/configJobs.lua`)

One entry per job with `canTrain`, `canTrainSkill`, `canBreed`, `canHeal`, `canBuyCubs`, `expBoost`, `maxPets`, and `maxBreeds`. The entry keyed by `Config.DebugAndBaseOption.defaultUnemployedJob` is the default for everyone else.

### Discord log (`cfg/configSrv.lua`)

`ConfigSrv.discordHook` is `true` in the default file and `ConfigSrv.webhookLink` is empty. Put your webhook in `webhookLink`, or set `discordHook = false`. `ConfigSrv.webhookLanguage` holds the log texts.

### Language lines added by updates

If you keep your own `cfg/configLang.lua` when you update, make sure it has these keys:

```lua
    notifyYouNeedHaveCalledPet = {"You need to have called pet", 2000},
    notifyAfterNextCallPet = {"After next call your pet will be renamed", 2000},
    notifyBallIsLossed = {"Your ball is lossed, you need to buy new one", 2000},
    notifyYourPetCantFetchBall = {"Your pet can't fetch ball", 2000},
    notifyThisItemCantHealYourPet = {"This item can't heal this pet", 2000},
    doYouWantTransferPetToPlayer = {"Player ID", "Accept"},
    promptPetTransfer = {"Transfer", 0x018C47CF},
    inputPetsPrice = {"Accept", "Price"},
    inputPetsDesc = {"Accept", "Description"},
```

The same applies to config keys added by updates: `ballItem` and `whistleKey` in `Config.PetsLogic`, and `canFetchBall` and `healItems` on each pet.

## Commands

| Command | What it does |
| --- | --- |
| `/loadPets` | Reloads the player's pets from the server |

## Customers also ran into these

### The pet shop blip shows but I cannot interact, and the console shows "attempt to index a nil value"

gumApi is missing or did not start. Install gumApi and make sure it starts before GUM Pets. Check the server console to see whether gumApi reports a problem on launch.

### The healing injection does not work

Check `canHeal` in `cfg/configJobs.lua` for the player's job. Players without it get the "You don't have permissions to do this" notification.

### Player-owned pet stores do not work

An old `pets_store` table does not match the current version, which stores `price`, `desc`, and `isBuyed` per listing. Update the script, delete the `pets_store` table in the database, and restart so the script creates a new one.

### `jobForBuy` does not restrict who can use the shop

Check the list. If `jobForBuy` contains `"unemployed"`, every player can buy that pet. List only the jobs that should have access. Older versions also had a bug in this check, so update to the current version.

### Shop blips or shop NPCs do not show up

Check `blipSetting.blipEnabled` and `npcSetting.enabled` for the shop in `cfg/configCoords.lua`. Older versions had bugs here, so update to the current version.
