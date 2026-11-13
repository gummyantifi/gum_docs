# GUM Ranch: troubleshooting

This page lists the ranch problems that support tickets and the changelog have already solved, plus the settings added in updates.

Most of it applies to the reworked ranch (beta from 2026-08-03, official release 2026-08-09). Entries about older versions say so.

## Check these first

1. gumApi is installed and starts before `gum_ranch`, and the name in your `server.cfg` is exactly `gumApi`. Ranch does not run without it. See the gumApi troubleshooting page.
2. After every update, add the new config and language lines from the changelog to your existing files. A product, blip, or language entry left in the old format causes most of the script errors below.
3. Replace every file the changelog entry names (client, server, and the files in the `html` folder). Several tickets were closed by nothing more than updating the client or server file.
4. If debug is off and you restart the script while in game, restart your game too. Ranch data only reloads live with `debug.clientEnabled` on.
5. Translate `configLang` before you buy any animals.

## Problems and fixes

### Install and startup

#### Console shows "attempt to call a nil value (field 'getInformation')"

The same cause produces `(field 'checkPay')` and `(field 'registerItem')`.

Cause: gumApi is missing, or it did not start before `gum_ranch`.

Fix: download gumApi from the GUM Scripts Discord, install it by its guide, and start it before the ranch script. gumApi is not included in the ranch download.

#### Server console shows "Couldn't find resource gumAPI."

Cause: the name in `server.cfg` does not match the resource folder. The resource is called `gumApi`, and `gumAPI` is a different name.

Fix: spell it in `server.cfg` exactly as the folder is named, save, and restart. In the ticket where this came up, the ranch also threw `attempt to index a nil value (field 'ranchCenter')` and `(field 'buyStore')` when a ranch was created in the Creator. Those errors stopped once gumApi started.

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

Cause: debug is off and the script was restarted while you were in game, so your client never reloaded the ranch data.

Fix: either restart your game after restarting the script, or enable `debug.clientEnabled` while you configure. With it on, you can stay in game and the data loads automatically. Turn `clientEnabled` off again before you open the server to players.

Two related facts from the same ticket and the changelog:

- The buy NPC only stands at ranches that are for sale. Once a ranch is bought, the NPC is gone (changelog 2025-04-17).
- While testing, a short update time such as `0.01` lets you watch how quickly animals lose hunger. Set it back to `15` afterwards.

#### Ranches look wrong or throw errors right after converting

Fix: after you run the convert command and restart the script, quit and rejoin the game.

The Ranch Creator guide gives the command as `/ranchcreator 50 convert`. The beta changelog entry of 2026-08-03 called it `/creator 50 convert`. Use the command from the guide.

#### An animal I disabled still shows in the buy menu

In the Ranch Creator, clear that animal's coordinates for the ranch. Since the 2026-08-04 update:

- If the animal section of a ranch is not filled in, that animal does not appear in the buy menu.
- If the maximum is 4 animals but only 2 positions are set, the limit is 2.
- If a trough has no coordinates, its purchase option is not shown.

On versions before the Creator, the changelog of 2025-02-21 gives the manual way: in `configRanchs`, change the animal entry from `chicken = {}` to `chicken = nil`.

#### Should I delete a ranch I no longer want?

Disable it in the Ranch Creator. The system relies on key indexes, so the changelog (2026-08-05) recommends disabling a ranch over deleting it.

### Products and production

#### The animal menu does not open, and the console shows "attempt to concatenate a nil value (field 'label')"

This was the most repeated config mistake in tickets. It also appears as an error when you walk up to the animals.

Cause: the `male` product is written in the old short format, for example `male = "fertilizer"`. Since 2026-08-06 a product is a table.

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
            takeProduct = 20,
            startCreatingProduct = 5,
        },
```

The stock config ships with `male = nil`, which means only females produce.

#### Products cannot be collected after I translated the language file

Cause: the script compares each animal's saved gender with the gender words in `configLang`. Animals bought before you translated those words are treated as the opposite gender. With `male = nil`, they produce nothing.

Fix: translate everything first, then buy the animals. Animals bought after the translation save the translated gender values and work correctly. The `js` update of 2026-09-16 also fixed the gender shown on purchase when the words are translated, so update `index.js`.

#### The collect option is shown but I cannot use it

Check these, in the order the developer asked them:

- The animal is not sleeping and not breeding. Products cannot be collected in either state.
- The animal is old enough to produce.
- A whole product exists. Production builds up in fractions, so nothing can be collected until the stored amount reaches 1.
- The stored amount has reached `takeProduct`, the minimum stored products required before they can be collected.
- The player is the owner or has the permission to collect.

#### Animals produce far less than before the update

To tune production, enable debug for the animals (the developer's example was the cow), set `updateTime` to about 1, and watch the server console. It shows how the animals produce on each update, so you can set the amounts quickly. When the amount per update is right, raise the update time again.

#### Male animals give no products

That is the default. `male = nil` disables products for males. Fill in `male` with the same table as `female` to enable it.

### Animals, saving and access

#### Animals and troughs are missing after I reconnect, but appear after a server restart

Cause: an animal loading problem in the builds before 2026-08-25.

Fix: update to the 2026-08-25 version or newer. That update adjusted the animal loading system, and the ticket was closed after it.

If animals still fail to spawn or suddenly disappear, check your F8 console for a spam of `creating ped` and report it. The developer asked for exactly that in the 2026-08-18 changelog entry.

#### Animal positions or shared access reset after a restart

Two causes showed up:

- The ranch saves on its update time, not at the moment you make the change. In one ticket the changes were lost because the player logged off before the next save. Stay on the server until a save has run.
- A server bug. The 2026-09-25 server update fixed reading and saving of `shared` (permissions) and `animalpos`. The 2026-08-11 update fixed ranches not saving during a txAdmin auto restart. Update the server file.

#### After the slaughter update, killed animals come back or animals stop responding

Cause: the slaughter feature (2026-06-28) added settings to every config file, and old configs were missing them. In the ticket, the ranches had no blip setting. From that update on, every ranch needs one:

```lua
        blipSprite = 423351566,--Blip for the ranch on the map.
```

Fix: add all of the new slaughter settings to your existing config files (see the 2026-06-28 entry below), then replace the client and server files. The ticket was closed after the server side was updated.

#### Errors about `expModifier`

Add this line to each animal (changelog 2025-08-26):

```lua
expModifier = 1.0
```

#### Errors right after adding new language lines

Check the commas. When you append lines to the language file, the line before them needs a trailing comma. The changelog entry of 2026-01-28 warns about this for the line after `[100]`.

## Settings added in updates

The config and language files were reorganized on 2026-08-03. Entries before that date use the old layout (numbered language lines, `configRanch` edited by hand).

- **2026-11-12 (v1.4.7)**, client/server. Added to `configBase.lua`: `Config.AnimalBehaviour` (`calm`, `directFollow`, `followLabel`, `distance`, `refreshSeconds`), `Config.OwnedRanchBlipsOwnerOnly = true`, and `Config.TransportPredators` (`enabled`, `chance`, `checkSeconds`, `cooldownSeconds`, `model`, `count`, `spawnDistance`, `despawnDistance`, `lifetimeSeconds`, `minimumTravel`).
- **2026-09-21**. Compatibility for custom animals. The changelog says to add the new animal to the database as a column of type longtext.
- **2026-09-17**, server/configSrv. Webhook added.
- **2026-09-17**. Old minigame restored. Add the `minigame` folder to the `html` folder, replace `index.css`, `index.html`, `minigame.js`, `minigameOld.js`, `fxmanifest.lua`, and add:

```lua
-- drag / click or false for disabled
Config.DisableMinigame = false

-- Control size of click radius in click minigame
Config.ClickRadius = 20
```

- **2026-08-25**. Gold currency for ranches (a checkbox in the Ranch Creator). New notification line: `notEnoughGold = "You don't have enough gold.",`
- **2026-08-24**. Added to `Config.MenuSetting`: `statsRecoveryWithoutAccess = false, -- Allow filling hunger/thirst/health of animals without ranch access`
- **2026-08-13**. Added `minAge` to the `breed` section of each animal in `configAnimals`:

```lua
breed = {
    blockIfIsMax = true,
    chance = 5,
    time = 60,
    minAge = 5, --- this part
},
```

- **2026-08-11**. New setting `Config.AnimalDebug > debugPrints = true`.
- **2026-08-10**. `Config.MaxHistoryLog = 30` in configBase (how many money history logs each ranch keeps, `false` for no limit).
- **2026-08-09**. `Config.happySleepEnabled = false` in configAnimals.
- **2026-08-08**. `Config.CustomAmountForGatherProducts = true` in configBase. New line in configLang inputs: `howMuchUWantTake = {"Confirm", "How many products do you want to gather?"},`
- **2026-08-06**. Product format changed in `configAnimals` from `female = {"Wool", "Wool"},` to a table with `itemId`, `label`, `tool`, `toolLabel`, `removeTool`, and `reduceDurability`. New notification lines: `youDontHaveTheRequiredToolToCollectProduct`, `yourToolIsBroken`.
- **2026-08-06**. Only players added to the ranch can lead animals.
- **2026-08-05**, index.js. New language line: `slaughter = "Slaughter : ",`
- **2026-08-05**. `Config.MoneyInRanchMax = 5000` in configBase. New notification line: `ranchMoneyLimitReached = "The maximum money limit has been reached $%s",`. Ranches can be disabled in the Ranch Creator.
- **2026-08-05**. Added to configBase: `Config.CanLeadDifferentAnimalTypesAtOneTime = true`, `Config.InteractionTime` (`clearPoopWithHand`, `rakeAnimation`, `cuddleAnimation`), `Config.Stealable` (`["Trash"]`, `["Products"]`). New notification lines: `troughNotificationFoodOrWater = {"food", "water", "heal"},` and `youCanLeadOnlyOneAnimalType`.
- **2026-08-05**. Added to configBase: `Config.BlockPickWithoutRake = true`, `Config.DisableMinigame = false`. New notification line: `youNeedRakeToCleanPoop`. New promptsKey line: `leadRadius = {"[F]", "F"}`.
- **2026-08-04**. New notification line: `youDontHaveTheRequiredJobToBuyThisRanch`. New line in configLang answers: `upgradeTroughConfirmation = {"Are you sure you want to upgrade the trough for $%s?", "Yes", "No"},`
- **2026-08-03**. For all animals, the `capacity` value in the `trough` section became a table of levels:

```lua
capacity = {
    [1] = {
        price = 100,
        model = "p_feedtrough01x",
        capacity = 200
    },
    [2] = {
        price = 200,
        model = "p_watertrough01x_new",
        capacity = 300
    },
    [3] = {
        price = 300,
        model = "p_watertrough02x",
        capacity = 400
    }
},
```

  New notification lines: `youHaveSuccessfullyUpgradedTheTrough`, `troughIsAlreadyAtMaxCapacity`. New promptsKey line: `troughUpgrade = {"Upgrade", 0x5734A944},`
- **2026-08-03**. Ranch Creator added and `configRanch` restructured. Convert an updated `configRanch` with the convert command. Animal positions and trough locations stay the same.
- **2026-07-17**. Set `fixedPrice` on a ranch to charge the full purchase price and then a recurring maintenance fee. Upload the `textAreaLine` and `border` images from the `html` folder.
- **2026-06-28**. Slaughter feature. Added to every animal in `configAnimal.lua`: `canKillAnimal`, `ageCapForKill`, `ageForKillModifier`, `expForKillModifier`, `productForKill`. Added to `configBase.lua`: `Config.KillAnimal = true` and `Config.KillAnimalSpots`. Added to `configLang.lua`: lines `[103]` to `[107]`. Every ranch needs a `blipSprite`.
- **2026-01-28**. `Config.MinigameEnabled = true` in cfgBase, language lines `[100]` to `[102]`. Later replaced by `Config.DisableMinigame` (2026-08-05).
- **2025-12-04**. `Config.EasyGather = 3.0` in `ConfigBase` (0 is disabled). The same position can no longer be used for multiple animals.
- **2025-10-21**. Admin command to delete the ranch you are standing in:

```lua
Config.AdminRole = "admin"       -- Admin role for ranch management
Config.AdminRemove = "removeranch" -- Admin command for removing a ranch
```

- **2025-10-03**. Cooldown for selling animals. New line: `[100] = "You need wait %d seconds."`
- **2025-08-26**. `expModifier = 1.0` added to the animals.
- **2025-06-11**. Separate product icons for males and females. Update the product icons in `html/images`.
- **2025-06-06**. New lines: `[98] = "You can have only 1 ranch.",` and `[99] = "You dont have job for buy ranch."`
- **2025-06-04**. `Config.trashClean = 30` and `Config.WanderAnimals = 3.0` in `configBase.lua` (0 = disabled). New lines `[96] = "Name"` and `[97] = "Remove"`. In `configAnimals.lua`, `product = "Milk"` was changed to `productFemale`, and `productMale` was added (`nil` = disabled).
- **2025-06-03**. `Config.BreedDebug = true` prints breeding stats to the server console.
- **2025-04-27**. `Config.AnimalPosition = "animal"` in `ConfigBase.lua` (command for custom animal positions, used as `/animal cow 1`). New lines `[87]` to `[95]` in `ConfigLang.lua`.
- **2025-04-21**. `Config.Only1Ranch = false` and line `[86] = "You have 1 ranch already bought",`
- **2025-04-17**. New lines `[84] = "Ranch : ",` and `[85] = {"$", "G"}`.
- **2025-02-21**. Set an animal to `nil` in `configRanchs` (for example `chicken = nil`) to disable buying it at that ranch.
- **2024-11-18**. `Config.BlipRanchColor = "BLIP_STYLE_AREA_FOW"`
- **2024-11-15**. `Config.TakeProductIfOld = true`. Added line `["Water_Bucket"] = {10, 20, "Water in bottle", "Empty_Water_Bucket"},`. The end of the entry now sets what is given back after filling a trough or animal.
- **2024-11-11**. `goldPrice` setting to sell specific ranches for gold.

*Built from the changelog and resolved support tickets. For anything not covered here, open a ticket in the GUM Scripts Discord.*
