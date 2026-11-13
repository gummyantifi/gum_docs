---
icon: wrench
---

# GUM Ranch: troubleshooting

This page lists ranch problems that support tickets and the changelog have already solved, checked against the current script. Setup and config are covered in the [Ranch Creator Guide](index.md).

## Check these first

1. gumApi is installed and starts before `gum_ranch`, and the name in your `server.cfg` is exactly `gumApi`. Ranch does not run without it. See the gumApi troubleshooting page.
2. After every update, compare your `cfg` files with the new ones and add the keys and language lines you are missing. The list at the end of this page shows the ones old configs most often lack.
3. Replace every file the changelog entry names (client, server, and the files in the `html` folder).
4. If `Config.AnimalDebug.clientEnabled` is off and you restart the script while in game, restart your game too. Clients only request ranch data on a resource start when it is on.
5. Translate `cfg/configLang.lua` before you buy any animals.

## Problems and fixes

### Install and startup

#### Console shows "attempt to call a nil value (field 'getInformation')"

The same cause produces `(field 'registerItem')`.

Cause: gumApi is missing, or it did not start before `gum_ranch`.

Fix: download gumApi from the GUM Scripts Discord, install it by its guide, and start it before the ranch script. gumApi is not included in the ranch download.

#### Server console shows "Couldn't find resource gumAPI."

Cause: the name in `server.cfg` does not match the resource folder. The resource is called `gumApi`, and `gumAPI` is a different name.

Fix: spell it in `server.cfg` exactly as the folder is named, save, and restart. Errors such as `attempt to index a nil value (field 'ranchCenter')` and `(field 'buyStore')` in the Creator went away in the same case once gumApi started.

#### oxmysql warns that gumApi ran a query with an oversized result set

```text
Warning: gumApi executed a query with an oversized result set (1302 results)!
SELECT * FROM items
```

The developer confirmed this warning is normal. Nothing to fix.

#### The update notes say to replace "js". Which file is that?

`index.js` in the `html` folder.

### Ranch Creator and setup

#### The NPC to buy a ranch does not appear, and only the Creator text is on the ground

Cause: `Config.AnimalDebug.clientEnabled` is off and the script was restarted while you were in game, so your client never reloaded the ranch list.

Fix: restart your game after restarting the script, or set `Config.AnimalDebug.clientEnabled = true` while you configure.

Two related facts:

- The buy NPC only stands at ranches that are for sale. Once a ranch is bought, the NPC is removed.
- While testing, a low `Config.updateTime` (the value is in seconds) lets you watch how quickly animals lose hunger. Set it back to `60*15` afterwards.

#### Ranches look wrong or throw errors right after converting

Fix: after you run the convert command and restart the script, quit and rejoin the game.

The command is `/creator 50 convert` with the default `Config.AnimalDebug.creatorPermission.command`. If your `cfg/configBase.lua` sets another command name there, use that name.

#### An animal I disabled still shows in the buy menu

In the Ranch Creator, remove that animal's positions for the ranch.

- If an animal has no positions at a ranch, it does not appear in that ranch's buy menu.
- If `shop.maxBuyCount` is 4 but only 2 positions are set, the limit is 2.
- If a trough has no coordinates, its purchase option is not shown.

If you use the manual `cfg/configRanchs.lua`, remove the animal's table from the ranch (or set it to `nil`) and run the convert command again.

#### Should I delete a ranch I no longer want?

Tick `Ranch disabled` in the Ranch Creator. Ranches are stored by key, so disabling a ranch is safer than deleting it.

### Products and production

#### The animal menu does not open, and the console shows "attempt to concatenate a nil value (field 'label')"

This also appears when you walk up to the animals.

Cause: the `male` product in `cfg/configAnimals.lua` is written in the old short format, for example `male = "fertilizer"`. A product is a table with a `label`.

Fix: write `male` in the same format as `female`, or set it to `nil` to disable products for males.

```lua
product = {
    female = {
        itemId = "Wool",
        label = "Wool",
        tool = nil,
        toolLabel = nil,
        removeTool = false,
    },
    male = {
        itemId = "Wool",
        label = "Wool",
        tool = nil,
        toolLabel = nil,
        removeTool = false,
    },
    max = 50,
    count = 2,
},
```

The stock config ships with `male = nil`, which means only females produce.

#### Products cannot be collected after I translated the language file

Cause: the script compares each animal's saved gender with `Config.Language.menu.male` and `Config.Language.menu.female`. Animals bought before you changed those words carry the old text. It matches neither word, so those animals build up no product.

Fix: translate everything first, then buy the animals. For animals that already exist, the gender text can be edited under `Modify Ranch` in the Ranch Creator.

#### The collect option is shown but I cannot use it

Check these:

- The animal is not sleeping. The gather prompt is disabled while it sleeps, and sleeping animals do not produce.
- The animal has reached `age.ageForProduct`.
- A whole product exists. Production builds up in fractions, and nothing can be collected until the stored amount reaches 1.
- If the product has a `tool`, the player carries that item.
- The player is the owner or has the `gatherProducts` permission. With `Config.Stealable["Products"] = true`, anyone can collect.

#### Animals produce far less than before the update

To tune production, set `Config.AnimalDebug.enabled = true`, list the animal under `Config.AnimalDebug.animals`, turn on `product` in `Config.AnimalDebug.show`, and lower `Config.updateTime`. The server console then prints each animal's values on every update. When the amount per update is right, raise `Config.updateTime` again.

#### Male animals give no products

That is the default. `male = nil` disables products for males. Fill in `male` with the same table as `female` to enable it. Males only produce when `female` is set as well.

### Animals, saving and access

#### Animals and troughs are missing after I reconnect, but appear after a server restart

Cause: an animal loading problem in the builds before 2026-08-25.

Fix: update to the 2026-08-25 version or newer.

#### Animal positions or shared access reset after a restart

- The ranch saves on `Config.updateTime`, when the resource stops, and on the txAdmin shutdown and scheduled restart events. It does not save at the moment you make the change. If the server process is killed before the next save, the change is lost.
- Older server files had a bug reading and saving `shared` and `animalpos`. Update the server file.

#### After the slaughter update, killed animals come back or animals stop responding

Cause: the config is missing the slaughter settings, or still has them in an older layout.

Fix: use the current layout. Each animal in `cfg/configAnimals.lua` has a `kill` table (`enabled`, `ageModifier`, `expModifier`, `products`), slaughter spots are in `Config.killSpot` in `cfg/configBase.lua`, and each ranch has a `blipSetting` table with `blipEnabled`, `blipSprite`, `blipColor`, and `blipName`. Then replace the client and server files.

## Keys that old configs often miss

Compare your files with these. Every key below exists in the current config.

- `cfg/configBase.lua`: `Config.AnimalBehaviour`, `Config.OwnedRanchBlipsOwnerOnly`, `Config.TransportPredators`, `Config.DisableMinigame`, `Config.ClickRadius`, `Config.CustomAmountForGatherProducts`, `Config.MoneyInRanchMax`, `Config.MaxHistoryLog`, `Config.CanLeadDifferentAnimalTypesAtOneTime`, `Config.InteractionTime`, `Config.Stealable`, `Config.BlockPickWithoutRake`, `Config.AnimalDebug.debugPrints`, and `statsRecoveryWithoutAccess` in `Config.MenuSetting`.
- `cfg/configAnimals.lua`: `Config.happySleepEnabled`, `minAge` in each animal's `breed`, the table format of `product.female` and `product.male`, `trough.capacity` as a list of levels (`price`, `model`, `capacity`), and the `kill` table.
- `cfg/configLang.lua`, `notifications`: `notEnoughGold`, `troughNotificationFoodOrWater`, `youHaveSuccessfullyUpgradedTheTrough`, `troughIsAlreadyAtMaxCapacity`, `youDontHaveTheRequiredJobToBuyThisRanch`, `youNeedRakeToCleanPoop`, `youCanLeadOnlyOneAnimalType`, `ranchMoneyLimitReached`, `youDontHaveTheRequiredToolToCollectProduct`, `yourToolIsBroken`.
- `cfg/configLang.lua`, other sections: `howMuchUWantTake` in `inputs`, `upgradeTroughConfirmation` in `answers`, `slaughter` in `menu`, `troughUpgrade` and `leadRadius` in `promptsKey`.
- `cfg/configSrv.lua`: the webhook settings.
- `html`: the `minigame` folder, `minigame.js`, and `minigameOld.js`.

These older keys are no longer in the script. The setting now lives here:

| Old key | Current key |
| --- | --- |
| `Config.Only1Ranch` | `Config.CanHaveOnlyOneRanch` |
| `Config.WanderAnimals` | `Config.TaskWanderInArea` |
| `Config.MinigameEnabled` | `Config.DisableMinigame` |
| `Config.KillAnimal`, `Config.KillAnimalSpots` | `kill.enabled` per animal, `Config.killSpot` |
| `canKillAnimal`, `productForKill` and the other per-animal kill keys | the `kill` table per animal |
| `productFemale`, `productMale` | `product.female`, `product.male` |
| `goldPrice`, `fixedPrice` on a ranch | `paySetting.ranchGold`, `paySetting.purchasePrice` |
| `Config.BlipRanchColor`, `blipSprite` directly on a ranch | `blipSetting.blipColor`, `blipSetting.blipSprite` |
| Numbered language lines such as `[100]` | Named keys in `Config.Language` |

For anything not covered here, open a ticket in the GUM Scripts Discord.
