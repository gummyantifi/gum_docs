---
icon: horse
---

# GUM Horses

GUM Horses is a horse ownership system for RedM. It covers genetics and breeding, training, horse needs, customization, auctions, and trading between players.

## At a glance

- Video: [GUM Horses on YouTube](https://www.youtube.com/watch?v=pEDthWjLpwg)
- Store: [GUM Horses on Tebex](https://gum-scripts.tebex.io/package/7806421)
- Resource name: `gum_horses`

## What it does

### Genetics and breeding

- Foals inherit genetics from both parents, including coat colors, patterns, and appearance.
- Statistics and personality traits are inherited too.
- Breeding requirements and timers are configurable.
- Each horse has a pedigree, so bloodlines progress over generations.

### Training and progression

- Horses earn XP and build a bond with their owner.
- Trainable stats: speed, acceleration, agility, courage, health, and stamina.
- There are multiple training activities, and the experience rewards are configurable.
- Progression is tracked per horse.

### Horse care

- Horses have hunger, thirst, energy, happiness, and health.
- Horses can get sick and recover.
- Metabolism is dynamic and the environment affects the horse.
- Horses age and pass through life stages.
- Feeding and care are configurable.

### Customization

- A Horse Creator for building a horse's look, with custom coat colors, patterns, and textures.
- Saddles, bridles, blankets, lanterns, and other accessories, with color options per component.
- Horseshoes, each with its own durability.

### Ownership, trading, and stables

- Wild horse taming.
- Horse leading and rope mechanics.
- Horse cargo and storage.
- Horse branding.
- Horse showcase and auctions.
- A player-to-player horse marketplace, plus gifting and transfers.
- Job and faction stables.
- Configurable stable locations and services.

### Other features

- Horse calling, with the horse's world position saved.
- Classic and Modern UI themes.
- Configurable interaction prompts.

### GUM Wagons

GUM Horses and GUM Wagons use the same stable layout (`jobPermissions`, `townArea`, store and "my" spots) and are built to run side by side. Draft horses for wagons are configured inside GUM Wagons, in its `configs/configDraftHorses.lua`. See [GUM Wagons](gum_wagons.md).

## Before you install

Install **gumApi** first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).

- Run the most recent version of gumApi.
- `fxmanifest.lua` declares two dependencies: `oxmysql` and `gumApi`. Both must start before `gum_horses`.
- GUM Horses is a new script built from the ground up. It is a separate product from `gum_stables`, which is still maintained.

Optional resources. The script checks whether they are started and works without them:

- `gum_houses`: parking horses at a property.
- `gum_farm`: produce items that are also horse feed are offered to `gum_farm` first.
- `gum_ecology`: records tamed wild horses.

## Install

1. Put the `gum_horses` folder in your resources and start it after `oxmysql` and `gumApi`.
2. Do not import any SQL. The script creates its tables on start and adds missing columns to existing ones.
3. Add the items listed below to your inventory.
4. Edit the files in `configs/`, then restart the resource.

### Database tables

Table names come from `Config.Database` in `configs/cfg.lua`. With the defaults, the script creates:

| Table | Holds |
| --- | --- |
| `gum_horses` | Owned horses |
| `gum_horses_breed` | Active breeding records |
| `gum_horse_components` | Bought and crafted components |
| `gum_horses_storage` | Forge horseshoe storage |
| `gum_horse_age_timer` | The aging tick counter |
| `gum_horses_market` | Player market listings |
| `gum_horses_market_payouts` | Pending payouts from market sales |

`Config.Database.table` is the base name. The `_breed`, `_storage`, `_market`, and `_market_payouts` tables are named from it. `componentsTable` and `ageTimerTable` are separate overrides.

If a table cannot be created, the server console prints `[gum_horses] Could not create database table #N`.

### Items

Item names are case sensitive. These are the defaults in the config files.

| Item | Used for | Where it is set |
| --- | --- | --- |
| `Brush` | Brushing a horse | Fixed in the script |
| `Apple`, `Hay`, `Sugar`, `Wild_Carrot` | Feed | Keys of `Config.Feeds` in `configs/cfgMetaAndStats.lua` |
| `Heal_For_Horse`, `Boost_For_Horse`, `Gold_For_Horse` | Stimulants | Keys of `Config.Feeds` |
| `HorseCartTag` | Renaming a horse | `Config.HorseManagement.rename.item` in `configs/cfg.lua` |
| `Horse_Shoe` | Horseshoes | Forge storage in the script, `Config.Horseshoe.outsideForge.item` |
| `Iron_Ore`, `Iron`, `Horse_Shoe_Casting` | Forge steps | `Config.Forge` in `configs/cfgSpots.lua` |
| `iron`, `leather` | Example component crafting recipe | `Config.Accesories` in `configs/cfgComponents.lua` |

Every key in `Config.Feeds` is registered as a usable item. If you add a feed, add the item with the same name. Set `Config.HorseManagement.rename.item = nil` to rename without an item. `Config.Branding.item` is `nil` by default, so branding needs no item unless you set one.

## Configuration

All config files are in `configs/`.

| File | What it holds |
| --- | --- |
| `cfg.lua` | Main settings: UI, prompts, admin role, database names, showcase, auction, branding, taming, cargo, locations, aging, stable management, jobs |
| `cfgSpots.lua` | `Config.Stables`, `Config.Forge`, `Config.TrainWhitelist` |
| `cfgHorses.lua` | `Config.Horses` (the horse catalog), `Config.HorseAppearanceCreator`, `Config.Descriptions` |
| `cfgGenetics.lua` | `Config.WildHorseGenetics`, `Config.Genetics` |
| `configTraits.lua` | `Config.Personality`, `Config.Traits` |
| `cfgMetaAndStats.lua` | `Config.BreedingTime`, `Config.Metabolism`, `Config.HorseTargetBars`, `Config.Horseshoe`, `Config.Bonding`, `Config.Exp`, `Config.Feeds` |
| `cfgComponents.lua` | Component camera, `Config.HorsePropsData`, `Config.HorseRope`, `Config.Accesories` |
| `cfgAnimalCargo.lua` | Carcass and pelt whitelists for horse cargo |
| `cfgHorseBody.lua` | `Config.HorseBodyEditor` (body proportions) |
| `cfgPlayerHorseMarket.lua` | `Config.PlayerHorseMarket` |
| `cfgLanguage.lua` | `Config.Language`: all text and prompt keys |
| `configServer.lua` | Server only: Discord webhook |
| `configDebug.lua` | `Config.Debug` switches |

`fxmanifest.lua` loads `configs/cfgLanguage.lua` (English). A Czech file, `configs/cfgLanguageCZ.lua`, ships in the folder and is not loaded by default.

### Main settings (`configs/cfg.lua`)

- `Config.Ui.style`: `'modern'` selects the dark theme. Any other value, including the shipped `'default'`, gives the classic parchment UI.
- `Config.Prompts.newPrompt`: `true` uses gumApi NUI prompts, `false` uses the original game prompts.
- `Config.KeyMapping`: adds the prompt shortcuts to the RedM key mapping settings, with the `prefix` shown there.
- `Config.adminRole`: the framework group allowed to use `/horsecreator` and `/wild`. Default `'admin'`.
- `Config.HorseCatalog.enabled` and `Config.MyHorseCatalog.enabled`: the catalog prompt in the shop and the quick list of owned horses.
- `Config.HorseShowcase`: `maxHorses = 6` per player and `maxPerStable = 6`. The display is removed when the owner goes further than `ownerDistance` (35.0).
- `Config.HorseAuction`: `enabled`, `clerkModel`, `interactionRadius`, `maxBid`. The clerk stands at the stable's `auctionSetting.position` if set, otherwise at `storeSetting.interaction`.
- `Config.Branding`: `stables`, `forges`, `onlyBred = true`, `minAge`, `maxAge`, optional `item`, and `marks` (one `code` and `label` per stable key).
- `Config.BreedingSpots.multipleBreed`: `true` gives each character a private pen. `false` lets one breeder occupy a pen for everyone.
- `Config.BreedingCall`: `allowMale` and `allowFemale` decide whether a horse can be called during an active breeding cycle. Both default to `false`.
- `Config.WildHorseTaming`: timing of the key prompts while breaking a wild horse.
- `Config.MustSelectHorse`, `Config.HorseSelectionCooldownMs`, `Config.SpamBlock`.
- `Config.HorseSpeedEffect`: applies the horse's 1 to 9 Speed attribute while riding, from `minMultiplier = 0.92` to `maxMultiplier = 1.08`.
- `Config.HorseCargo`: carcasses and pelts on a called horse (`maxLargeAnimals = 1`, `maxSmallAnimals = 2`, `defaultPeltStorage = 3`).
- `Config.HorseLocations`: `keepAtAssignedStable`, `saveWorldPosition`, `stableSpawnRadius`, and `transferCooldowns` (60 seconds from another stable or from the world, `0` is instant).
- `Config.SimulateNodes`: fixed spawn points for places where the nearest road node is unsuitable.
- `Config.Age`: `tickIntervalMinutes = 60`, age gained per tick for foals and adults, `allowFoalPurchase`, `canDieOnOld`, `deathChancePerTick = 20`. Each horse's `maxAge` is set in `cfgHorses.lua`.
- `Config.HorseManagement`: `rename`, `gift`, `stableCare` (rest and paid feeding), `stableHealing` (price 10, revive price 100), and `sale` (base prices, `maxPrice = 500`, bonuses for EXP, bonding, and genetics).

### Jobs (`Config.Jobs` in `configs/cfg.lua`)

Each key is a job name. The `["default"]` entry applies to every job that is not listed. Rename the example `["Horse_Trainer"]` entry to your trainer job.

Main keys per job:

- `maxHorses`, `maxCalledHorses`
- `canInspectBags`, `canFlee`, `canShowcase`, `canAuction`, `canSaveTame`
- `showInfo` (`brand`, `stats`, `name`): what this job sees when targeting a horse
- `pedigree`: also needs `Config.Genetics.pedigree.enabled`
- `wildHorses` (`canTame`, `canSell`, `canStore`, `canViewStats`)
- `horseSale`: sale multipliers for `normal`, `wild`, `young`, `old`, `dead`
- `expModifier`, `expBondingModifier`
- `appearance` (`enabled`, `minScale`, `maxScale`)
- `training` (`glassing`, `dance`, `jump`, `rear`, `footscratch`, `longing`)
- `obstacleCourse`: a route of `start`, `jump`, and `finish` points with XP per jump

Saving a tamed wild horse needs both `canSaveTame = true` and `wildHorses.canStore = true` for the job.

### Stables and forges (`configs/cfgSpots.lua`)

`Config.Stables` is keyed by stable name. The default file has Valentine, Blackwater, Rhodes, Saint Denis, Annesburg, Strawberry, and Tumbleweed. Each stable has:

- `townArea`: the area in which a dismissed horse returns to this stable.
- `shopBlip`: `enabled` and `name`.
- `jobPermissions`: `access` (jobs allowed in, empty allows everyone), `openClose` (an allowed job can open and close the stable), `fraction` (shared horses for the jobs in `access`).
- `returnHorse`: `enabled`, `price`, `currency` (`"money"` or `"gold"`).
- `storeSetting`: `interaction`, `horse`, `spawnHorse`, `wildHorse`.
- `mySetting`: `interaction`, `horse`.
- `showcaseSetting`: `enabled`, `position`, `radius`.
- `auctionSetting` (optional): `position` and `model` for the auction clerk.
- `componentSetting`: `interaction`.
- `breedSetting`: the breeding pens.

In the default file, Blackwater, Rhodes, and Saint Denis share the same four `breedSetting` coordinates. Set real pen positions for every stable where you want breeding.

`Config.Forge` defines a forge per key: `baseSetting.job` (the jobs that can work there), three work steps (`ovenSetting`, `anvilSetting`, `grindSetting`, each with `startItem` and `toItem`), and `storageSetting` for horseshoe storage. The default chain is `Iron_Ore` to `Iron` to `Horse_Shoe_Casting` to `Horse_Shoe`.

### Horse catalog (`configs/cfgHorses.lua`)

`Config.Horses` has one entry per horse model. Main keys:

- `enabled`: `false` hides the horse from shops.
- `buyBlocked`, `tameBlocked`, `saveWildBlocked`
- `stables`: stable names where it is sold. Empty means every stable.
- `category`, `color`, `maxAge`
- `dollarPrice`, `goldPrice`, `sellPrice`, `sellWildPrice`
- `storage`, `canUseLoadStorage`
- `canUseShoe`, `canBreed`
- `jobLock`: jobs allowed to buy. Empty allows everyone.
- `identifierForBuy`: see below.
- `appearance` (optional): a coat preset. `/horsecreator` generates this block for you.

`Config.Descriptions` holds the text shown in the purchase menu, keyed by `category`.

### Restrict horse purchases to specific accounts

Add `identifierForBuy` to a horse entry in `configs/cfgHorses.lua`, next to `jobLock`:

```lua
identifierForBuy = {
    'steam:110000112345678',
},
```

- A missing, `nil`, `false`, or empty `{}` value lets everyone buy.
- Full `steam:...` values and raw hex IDs are both accepted, in any letter case. Always quote them as strings.
- Job and stable rules still apply.
- It only affects shop purchases. Owned horses, breeding, gifts, auctions, and player market sales are not affected.

### Genetics, traits, and needs

- `configs/cfgGenetics.lua`: `Config.Genetics.enabled` is the master switch. `maleChance`, `inheritanceChance`, `betterGeneChance`, `mutationChance`, and `maxStats` (the cap per stat) shape breeding. `pedigree.maxGenerations` is capped at 8 by the server. `Config.WildHorseGenetics` sets the bonus roll for tamed wild horses.
- `configs/configTraits.lua`: `Config.Personality` defines the personality traits and their effects. `Config.Traits` controls trait training: by default only foals can train (`foalsOnly = true`, below `adultAge = 5.0`), with `dailyXpLimit = 300` per horse.
- `configs/cfgMetaAndStats.lua`: `Config.BreedingTime`, `Config.Metabolism` (each need can be turned off under `needs`), `Config.Horseshoe` (wear and stamina effects), `Config.Bonding`, `Config.Exp`, and `Config.Feeds`.

### Body proportions (`configs/cfgHorseBody.lua`)

`Config.HorseBodyEditor` adds a Body proportions section to `/horsecreator` and to the owned horse Appearance editor. Set `enabled = false` to turn it off. Horses that already have saved proportions keep them. Each feature has a `label`, a `group`, and a `min`, `max`, and `step`. Values run from -1 to 1, and 0 keeps the model's original shape. No SQL change is needed.

### Player horse market (`configs/cfgPlayerHorseMarket.lua`)

`Config.PlayerHorseMarket` has `enabled`, `minPrice`, `maxPrice`, `maxDescriptionLength`, a `blip`, and a `markets` table. Each market key must match a `Config.Stables` key. Per market: `enabled`, `jobLock` (jobs that can manage listings), `interaction`, `displayHorse`, and `camera`. Anyone can browse and buy. Give the market its own coordinates so it does not overlap the stable prompts.

### Discord log (`configs/configServer.lua`)

Set `ConfigSrv.discordHook = true` and put your webhook in `ConfigSrv.webhookLink`. `ConfigSrv.discordLogs` turns each event on or off: `horsePurchase`, `horseSale`, `horseRename`, `horseGift`, `horseAppearance`, `horseMarketListing`, `horseMarketWithdrawal`, `horseMarketPurchase`.

### Debug (`configs/configDebug.lua`)

`Config.Debug` has `Client`, `Server`, `JS`, `HorseAppearance`, `TraitTraining`, and `DrawTexts` switches. `Login = true` also initializes players who are already connected after a resource restart. Restart the resource after changing them.

### Prompt keys and text (`configs/cfgLanguage.lua`)

All text is in `Config.Language`. Prompt labels and keys are in `Config.Language.promptKeys`, for example `catalog = { 'Horse catalog', 'F' }`. A key can be a key name or a control hash.

## Commands

| Command | Who | What it does |
| --- | --- | --- |
| `/horsecreator` | Players in the `Config.adminRole` group | Opens the Horse Creator. It exports a `Config.Horses` entry with the `appearance` block. |
| `/wild` | Players in the `Config.adminRole` group | Makes the horse you are riding wild. |
| `/auction [stable name]` | Everyone, when `Config.HorseAuction.enabled` is `true` | Opens the auction panel. Hosting an auction needs `canAuction = true` for the job. |

`Config.HorseAppearanceCreator.command` in `configs/cfgHorses.lua` adds a second command name for the creator. `/horsecreator` always stays available.

## Updating by hand

If you keep your own config files when you update, check these:

- `fxmanifest.lua` must load `configs/cfgHorseBody.lua` in `shared_scripts`.
- `Config.Language.ui` in `configs/cfgLanguage.lua` needs these lines for the creator camera and the body editor:

```lua
        horseCreatorCamera = 'Camera',
        horseBodyTitle = 'Body proportions',
        horseBodyHint = "Adjust individual features. Zero keeps the model's original shape.",
        horseBodySearch = 'Find a feature',
        horseBodyReset = 'Reset body',
```

- `identifierForBuy` is optional. Horse entries without it stay open to everyone.

## Customers also ran into these

### The auction NPC is in the wrong position when the auction is disabled

This was a bug in the script, fixed in v1.0.18. Update to v1.0.18 or later. The clerk position itself comes from the stable's `auctionSetting.position`, or `storeSetting.interaction` if that is not set.
