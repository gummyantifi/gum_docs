---
icon: cow
---

# Ranch Creator Guide

This page covers what `gum_ranch` needs, where its config files are, and how to build ranches with the in-game Ranch Creator. For errors and fixes, see [GUM Ranch: troubleshooting](troubleshooting.md).

## What the script needs

- gumApi. The script gets its API through the `getApi` event and uses it for player data, items, notifications, prompts, and storage. Start gumApi before `gum_ranch`.
- oxmysql. All ranch data is read and written through `exports.oxmysql`.
- Optional: `gum_farms`. When it is running and `Config.gumFarming` is `true`, players can fill the water side of a trough from a filled farming bucket.
- Optional: `gum_skills`. When it is running, the ranch reads the `moreMoney` skill point and adds experience under the `gum_ranch` skill.

## Install

1. Put the `gum_ranch` folder in your resources and start it after gumApi and oxmysql.
2. You do not need to import SQL. On start, the script creates the `ranch` table if it is missing and adds the `animalpos`, `troughpos`, and `money_history` columns to an older table.
3. Add the items the config uses to your items. The stock config uses these item IDs:

| Used for | Item IDs in the stock config |
| --- | --- |
| Rake and manure | `Rake`, `Fertilizer` |
| Feed | `Evergreen_Huckleberry` (cow), `Grain` (chicken), `Corn` (sheep), `Apple` (goat), `Wild_Carrot` (horse), `Potato` (pig) |
| Water | `Clean_Water_Bottle`, `Water_Bucket` (gives back `Empty_Water_Bucket`) |
| Healing | `Health_For_Dog` |
| Products | `Milk`, `Egg`, `Wool`, `Goat_Milk`, `Leather`, `Pork_Meat` |
| Product tool | `Watering_Can` (needed to collect `Milk`) |
| Slaughter | `Meat` |

4. Translate `cfg/configLang.lua` before anyone buys animals. The script saves each animal's gender as the text in `Config.Language.menu.male` and `Config.Language.menu.female`, then compares against those values later.
5. Create your ranches (see below).

### Custom animals

The Creator and the database loader build their animal list from `Config.animalSetup`. The `ranch` table is only created with the six stock columns (`chicken`, `cow`, `sheep`, `goat`, `horse`, `pig`). If you add an animal to `Config.animalSetup`, `Config.MaleModel`, and `Config.FemaleModel`, also add a `longtext` column with the same name to the `ranch` table.

## Commands

| Command | Who | What it does |
| --- | --- | --- |
| `/ranch` | The owner and players added to the ranch | Opens the menu of the ranch you are in. Rename it with `Config.OpenRanch`. |
| `/creator <distance>` | Groups in `Config.AnimalDebug.creatorPermission.groups` (`admin`, `superadmin`) | Opens the Ranch Creator. |
| `/creator <distance> convert` | Same groups | Rebuilds `data.json` from `cfg/configRanchs.lua`, then opens the Creator. |

The Creator command name comes from `Config.AnimalDebug.creatorPermission.command` in `cfg/configBase.lua`. The default is `creator`. If your config has a different value there, use that name in every command on this page.

## Config files

All config files are in the `cfg` folder.

### `cfg/configBase.lua`

General settings. The main keys:

- `Config.updateTime`: seconds between ranch updates and saves. Default `60*15`.
- `Config.OpenRanch`: the menu command. Default `"ranch"`.
- `Config.gumFarming`: allow filling troughs with the farming water bucket. Default `true`.
- `Config.FertilizerID`, `Config.RakeID`, `Config.RakePenalty`, `Config.BlockPickWithoutRake`, `Config.StackFertilizer`: manure cleanup. Without a rake the amount is multiplied by `Config.RakePenalty` (default `0.5`).
- `Config.CanLeadDifferentAnimalTypesAtOneTime`, `Config.AnimalBehaviour`, `Config.TransportPredators`: leading animals, follow behaviour, and predator encounters while leading animals outside the ranch.
- `Config.OwnedRanchBlipsOwnerOnly`: when `true`, an owned ranch's blip is shown only to its owner.
- `Config.InteractionTime`: seconds for `clearPoopWithHand`, `rakeAnimation`, `cuddleAnimation`.
- `Config.PrepayRanchMaxTime` (default `4`) and `Config.PayDayTax` (default `7`): how many weeks of rent can be prepaid and how many days pass between rent payments.
- `Config.TaskWanderInArea`: wander distance in meters, or `false`.
- `Config.DisableMinigame` and `Config.ClickRadius`: set `Config.DisableMinigame` to `"click"` or `"drag"` to use a gathering minigame. With `false` or `true`, products are gathered without a minigame.
- `Config.CustomAmountForGatherProducts`: ask the player how many products to take.
- `Config.MoneyInRanchMax`: money limit per ranch, or `false`.
- `Config.MaxHistoryLog`: how many money history entries each ranch keeps, or `false` for no limit.
- `Config.CanHaveOnlyOneRanch`, `Config.SplitTrought`, `Config.transferAnimalOnAnotherRanch`.
- `Config.MenuSetting`: menu toggles (`sellAnimals`, `buyAnimals`, `changeAnimalPos`, `changeTroughPos`, `detailedStats`, `barStatsOnAnimals`, `storage`, `statsRecoveryWithoutAccess`). `storage` is `false` or the storage capacity.
- `Config.PermissionSettings`: which actions need an explicit permission for players added to a ranch (`buy`, `sell`, `manageAccess`, `manageStorage`, `prepay`, `pickTrash`, `gatherProducts`, `withdrawMoney`, `depositMoney`, `transferAnimals`, `sellRanch`).
- `Config.Stealable`: whether players without access can take `["Trash"]` and `["Products"]`.
- `Config.sellSpot`, `Config.buyAnimalSpot`, `Config.killSpot`: world spots for selling, buying, and slaughtering led animals. Each entry has `blipSetting` and `baseSetting.coords`. Sell and buy spots also have `npcSetting`.
- `Config.AnimalDebug`: debug prints, the Creator command and groups, and `clientEnabled`.

`Config.AnimalDebug.clientEnabled` makes each client request ranch data when the resource starts. Keep it on while you configure so a script restart reloads your client. Without it, the ranch list is sent to a client when its character loads.

### `cfg/configAnimals.lua`

- `Config.MaleModel` and `Config.FemaleModel`: ped model per animal.
- `Config.geneticsChance`, `Config.geneticsName`, `Config.experienceLevels`: genetics and experience multipliers.
- `Config.happyEnabled`, `Config.happySleepEnabled`, `Config.DisableDieOnAge`, `Config.DiePenaltyForSellPrice`.
- `Config.animalSetup`: one table per animal with `shop` (prices, `minSellAge`, `maxBuyCount`, `maxBreedCount`), `age`, `experience`, `satisfaction`, `hunger.foodData`, `thirst.waterData`, `health.data`, `product`, `fertilizer`, `breed`, `trough`, and `kill`.

A product is a table. `male = nil` (the stock value) means males produce nothing:

```lua
product = {
    female = {
        itemId = "Milk",
        label = "Milk",
        tool = "Watering_Can",
        toolLabel = "Milk Bucket",
        removeTool = true,
        reduceDurability = false
    },
    male = nil,
    max = 50,
    count = 2,
},
```

Slaughter is set per animal in `kill` (`enabled`, `ageModifier`, `expModifier`, `products`) and is off in the stock config. Trough levels are in `trough.capacity`, a list of `{price, model, capacity}` levels.

### `cfg/configLang.lua`

`Config.Language` with `notifications`, `inputs`, `answers`, `menu`, `prompts`, and `promptsKey`. `promptsKey` holds the label and control hash of each prompt.

### `cfg/configSrv.lua`

Server only. `ConfigSrv.discordHook`, `ConfigSrv.webhookLink`, `ConfigSrv.webhookColor`, and the log texts in `ConfigSrv.webhookLanguage`.

### `cfg/configRanchs.lua` and `data.json`

The script reads ranch definitions from `data.json` in the resource folder. The Creator writes that file. `cfg/configRanchs.lua` (`Config.Ranch_Coords`) is the manual format and is only used by the convert command.

## Two ways to create a ranch

### 1. Ranch Creator

The recommended way is the in-game Ranch Creator.

```text
/creator 50
```

`50` is the creator distance. It is stored as the fourth value of the ranch center when you set that field. Use a larger number for larger ranches.

### 2. configRanchs.lua

You can also write ranches by hand in `cfg/configRanchs.lua`. After every change to that file, convert it:

```text
/creator 50 convert
```

This rebuilds `data.json` from `Config.Ranch_Coords`. It replaces the whole ranch list, so ranches that exist only in the Creator are lost. You only need `convert` when you use the manual file.

## Opening the Creator

```text
/creator 50
```

The Creator opens if your group is listed in `Config.AnimalDebug.creatorPermission.groups`. Press ESC to close it.

## Creating a ranch

Select `Create New Ranch`, then fill in the ranch.

### Basic settings

- Ranch name: name of the ranch.
- Job: job required to buy the ranch. Leave empty for no job.
- Ranch disabled: hides the ranch blip and the buy prompt.
- Clearing default npcs and animals: removes the game's own NPCs and animals around the ranch.
- Teleport animal coords: animal teleport position.

### Ranch coordinates

Coordinate fields take your current position. Stand where you want the position and click the field. A field is only filled when it is empty, so clear it first if you want to set it again.

- Coords for buy: the buy NPC and purchase spot.
- Ranch center: center of the ranch.
- Lead cancel: where leading is cancelled.

### NPC

Enter the NPC model in `NPC Model`. Then stand where you want the NPC and click `Coords for buy`.

### Blip

Enable `Ranch Blip` and set the blip name, blip sprite, and blip color. The stock ranches use `blip_ambient_herd` as sprite and `BLIP_STYLE_AREA_FOW` as color.

### Payment

Set a rent price, a purchase price, or both. If a purchase price is set, the buyer pays that price. Enable `Gold pricing` to charge gold.

## Animal positions

The Creator shows one `Set <animal> coords` button for each animal in `Config.animalSetup`. Stand where the animal should spawn and click the button. You can add several positions per animal. Click an existing position to remove it.

In the buy menu, the limit for an animal is the lower of `shop.maxBuyCount` and the number of positions you set. An animal with no positions does not appear in the ranch's buy menu.

## Troughs

Each animal has its own trough field (`Cow trough`, `Chicken trough`, and so on). Stand where you want the trough and click the field. Your position and heading are inserted. If an animal has no trough position, the ranch menu does not offer a trough for it.

## Preview

While you configure a ranch, the Creator draws its positions in the world: the ranch center with the ranch settings, each animal position, each trough, `Teleport coords`, `Buy NPC/SPOT`, and `Lead Cancel`.

## Saving

Click `Create/Save Ranch`. A new ranch gets the next free `key` automatically. The list is written to `data.json` and sent to all players.

## Editing and deleting

Select an existing ranch from the list, change its settings, and click `Create/Save Ranch`.

To remove it, click `Delete Ranch`. Ranches are stored by key, so disabling a ranch with `Ranch disabled` is safer than deleting it.

## Modify Ranch

`Modify Ranch` edits the saved data of an owned ranch:

- Money and prepaid amount.
- Each animal's name, gender, age, hunger, thirst, health, satisfaction, product, sleep time, experience, and genetics.
- Each trough's food and water.

You can also add and delete animals from this menu.
