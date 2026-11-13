---
icon: wrench
---

# GUM Stables: troubleshooting

This page lists `gum_stables` problems that have a known fix, plus the config lines, translation lines, and files that updates added. Install steps and the config overview are in the [guide](index.md).

All config files are in the `cfg` folder: `config.lua`, `configCoords.lua`, `configHorses.lua`, `configCarts.lua`, `configJobs.lua`, `configLang.lua`, `configSrv.lua`. Texts are in `Config.Language` (`cfg/configLang.lua`). Discord log texts are in `ConfigSrv.webhookLanguage` (`cfg/configSrv.lua`).

## Check these first

1. Old `horses`, `horses_breeding`, and `wagons` tables. If these tables are left over from another stables script, drop them and restart `gum_stables`. The script creates its own tables.
2. `oxmysql` and gumApi are installed and start before `gum_stables`. On gumApi versions older than the 2026-10-20 update, the framework also has to be set in the gumApi config: a wrong or unset framework there produces nil value errors in `gum_stables`. Newer gumApi versions need no framework setting.
3. Your config files have the lines added by the update you installed. The script files are replaced on update, but your `cfg` files are yours, so new keys have to be added by hand. A missing key is the usual reason a feature stops working right after an update. See [Settings added in updates](#settings-added-in-updates).
4. You can see the gumApi download. It is only visible with the customer role in the GUM Scripts Discord.

The changelog numbers releases as 1.2.x. The `version` line in `fxmanifest.lua` is not kept in step with those numbers, so identify your build by the changelog date and by the files and keys listed below.

## Problems and fixes

### Installation and database

#### The script has no SQL file

There is none to import. `gum_stables` creates its tables when it starts: `horses`, `wagons`, `horses_breeding`, `horsestrainers`, `horses_saddles`, `horsesstore`, `wagonstore`, and `real_logic_horses`. It also adds missing columns on every start. Install `oxmysql` and gumApi first, remove any old `horses`, `horses_breeding`, and `wagons` tables, then start `gum_stables`.

#### The console shows "gum_stables was unable to execute a query" with "Unknown column"

Example:

```
[script:oxmysql] Error: gum_stables was unable to execute a query!
[script:oxmysql] Query: ALTER TABLE `horses_breeding` ADD COLUMN IF NOT EXISTS `reward_outfit` LONGTEXT DEFAULT NULL AFTER `reward_id`;
[script:oxmysql] Unknown column 'reward_id' in 'horses_breeding'
```

The `reward_id` column is added by another query in the same startup batch, so this exact message can appear on the first start with a new `horses_breeding` table. Restart `gum_stables` once.

If the error is still there after a restart, or it names other columns, the database has `horses`, `horses_breeding`, or `wagons` tables from another stables script and their columns do not match. Delete those tables and restart `gum_stables`. Dropping a table removes the horses and wagons stored in it, so do this on a fresh install, or back the tables up first.

#### Buying a wagon or horse gives a database error

Same cause and same fix as above: remove the old `horses` and `wagons` tables and restart the script.

If your server runs a newer MariaDB version, also update to the 2026-01-11 release or later. From that release the server creates certain values in SQL as a fallback, which fixed purchases of horses and carriages failing on newer MariaDB.

#### "You lack the required entitlement to use gum_stables" and "Couldn't start resource gum_stables"

Restart the complete server so the license access reloads.

#### I cannot find the gumApi download

The gumApi download is in a channel that only shows once you have the customer role in the GUM Scripts Discord. Open a role request ticket and give the Tebex transaction ID from your purchase email. If you left the Discord and rejoined, the role is gone and has to be requested again. A developer who works on your server gets access when the script owner opens a ticket and asks for a developer role for them.

### gumApi and start order

#### gum_stables does not start or does not work after install

`gum_stables` needs gumApi and `oxmysql`. Start both before `gum_stables`, and set your framework in the gumApi config if your gumApi version has that setting.

#### "attempt to call a nil value (field 'registerItem')"

`gum_stables` calls `registerItem` from gumApi when it starts. Cause: the framework in the gumApi config is not set to the framework your server runs, or gumApi started after `gum_stables`.

Fix: change the framework in the gumApi config, check the start order, then restart.

### After an update

#### Players can no longer transfer or give a horse to another player

Cause: the 2026-03-05 update added a per-job permission for the horse "Transfer" prompt. If the key is missing from a job, that job does not get the prompt. The key is not in the shipped `cfg/configJobs.lua`, so it is missing on a fresh install too.

Fix: add this line to every job in `cfg/configJobs.lua` that should be able to transfer (including the `unemployed` entry if everyone should), then restart the script or the server:

```lua
canTransferToPlayer = true, -- Can transfer horse to player
```

Since version 1.2.1 (2026-10-30) the giver and the recipient also have to be close to each other, for horses and for wagons. The maximum distance is set in `cfg/configSrv.lua`:

```lua
-- Maximum distance in meters between the giver and recipient for horse/wagon gifts.
ConfigSrv.playerTransferRadius = 5.0
```

When the other player is too far away, the giver sees line `[81]` of `Config.Language` ("Selected player is not close enough. Maximum distance: ").

#### Foals cannot be saved to the stable after breeding

Cause: the 2025-02-27 update needs four new lines in `ConfigSrv.webhookLanguage` (`cfg/configSrv.lua`).

Fix: add them.

```lua
[98] = "Save breeded horse",
[99] = "Name : ",
[100] = "Model : ",
[101] = "Gender : ",
```

#### Horse death notifications do not reach Discord

The 2026-04-14 update added two lines for the horse death webhook. They belong in `ConfigSrv.webhookLanguage` in `cfg/configSrv.lua`. If you put them in `cfg/configLang.lua`, move them:

```lua
[103] = "The horse died of old age.",
[104] = "The horse died due to player error.",
```

Also check that `ConfigSrv.discordHook = true` and that `ConfigSrv.webhookLink` is your own webhook. Update client and server to the 2026-04-17 release or later, which fixed the messages not being sent.

#### Drinking from troughs stopped working after the 2025-01-23 update

That update changed troughs from one-sided to two-sided and requires a config change. Replace your `["drinkProps"]` section (in `Config.HorseLogic.statusSetup.metabolism`, `cfg/config.lua`) with the new format:

```lua
["drinkProps"] = {
    [GetHashKey("p_watertrough02x")] = {
        front = {0.0, -1.1, 0.0},
        back = {0.0, 1.1, 0.0},
    },
    [GetHashKey("p_watertrough01x")] = {
        front = {0.0, -1.1, 0.0},
        back = {0.0, 1.1, 0.0},
    }
},
```

#### Errors after the 2025-07-19 update

That update added `protectColoringByVip()` to `clientCustom.lua`. The script calls it every time it loads job permissions, so the function has to exist even if you do not use VIP coloring:

```lua
function protectColoringByVip()
    local isVipEnabled = false
    if isVipEnabled then
        -- Check if the player is in a specific group
        -- If not in an allowed group, return false
        -- return true/false
    else
        return false
    end
end
```

#### Errors after updating to version 1.2.7

Version 1.2.7 (2026-11-12) is more than a file swap. Check all of these:

- `server.lua`, `fxmanifest.lua`, and `client.lua` are replaced.
- The new `fxmanifest.lua` loads `cfg/purchaseAccess.lua` and `horseCleanup.lua`. Both files have to be in the resource.
- The new lines are added to `cfg/configHorses.lua`, `cfg/configCarts.lua`, `cfg/configLang.lua`, and `cfg/config.lua` (listed under [Settings added in updates](#settings-added-in-updates)).

#### My custom slider styles broke after the 2026-02-18 update

That update replaced the slider styles and added the image `html/images/bordelStyle.png`. The new CSS is at the bottom of `index.css`. If you use custom graphics, remove these parts from `index.css`: `.slider`, `.slider::-webkit-slider-thumb`, `.slider::-moz-range-thumb`.

### Horses and wagons

#### Horses or wagons do not spawn after login on VORP

With `respawnAfterLogin` enabled, horses and wagons did not spawn on the VORP framework. Fixed in the 2025-03-03 update (client and server).

#### A horse spawns unpainted, or components load in the wrong color

Color and component loading was reworked several times (2025-06-10, 2025-06-16, 2025-11-04). The 2025-11-04 update made component loading faster and more reliable, so a whistled horse is colored as set in the stables. Update client and server to that release or later.

#### Horses from faction (company) stables do not spawn

Fixed in the 2025-09-24 update (`client.lua` and `clientTag.lua`).

#### The camera gets stuck when leaving the stables or a player shop

Fixed in the 2025-08-19 client update.

#### The wheel or repair minigame stays open on screen

Fixed in the 2025-09-24 update.

#### Courage, friendliness, or experience drop on their own

Fixed in the 2025-12-10 and 2025-12-12 updates (client and server). The second one also fixed quick pick overwriting those values on your horse.

#### Age or EXP shows long decimals in the menu (for example 25.00000002)

Fixed across the 2026-01-05, 2026-01-28, and 2026-02-06 updates to the js files. Replace `html/index.js` with the current one.

#### Server error "horseColoring.horseColorPal"

Fixed in the 2026-06-22 server update.

#### A horse removed from `configHorses.lua` causes errors

Selling a horse that was removed from the config was fixed on 2024-11-18. Ageing a horse that was removed from `cfg/configHorses.lua` is protected since version 1.2.6 (2026-11-11): such a horse falls back to `maxAge` from `Config.HorseLogic.ageingSetup`. Replace `server.lua`.

#### A horse bought through Tebex does not age

This is a setting. Horses given with the Tebex flag are marked in the `is_tebex` column of the `horses` table (added by the 2026-04-20 update). `tebexHorsesAge` in `Config.HorseLogic.ageingSetup` decides what happens to them. The default `false` means they do not age and do not die of old age. Set it to `true` to let them age.

### Messages that come from a setting

These are not bugs. Each message below is shown because a config option is switched on or a job permission is off. The quoted texts are the shipped English lines of `Config.Language`.

#### "You cant summon horse in this area." or "You cant summon cart in this area."

Whistling is blocked in the towns listed in `["cityCallBlock"]` (in `Config.HorseLogic` and `Config.CartLogic`). Set `enabled = false` or remove the town from `bannedTowns`. The shipped config has it off for horses and on for wagons.

#### "You cant open storage while cart is damaged."

`blockStorageWhileDamaged = true` in `Config.CartLogic.statusSetup` blocks wagon storage while the wagon is broken.

#### "Your horse cannot graze in cities."

The horse is inside one of the areas in `["grazeBlockLocations"]` (`Config.HorseLogic.statusSetup.metabolism`). An empty list disables the block.

#### "This horse cannot be purchased in the shop."

The horse has `seeIfJobBlock = true`, so players see it in the shop but cannot buy it unless they have a job from its `jobForBuy`.

#### "This horse or wagon is not available for your account."

The horse or wagon has entries in `identifierForBuy`. Only the listed Steam identifiers can buy it (`'steam:11000010abcdef01'` or the bare hex). An empty list allows everyone. Job and group rules still apply.

#### "You cant sell wild horse to stables." or "You cant sell or save this horse."

The first comes from the job permission `canSellTame`. The second is shown when the job does not have `canSaveTame` (since version 1.2.2), when `canSaveWildHorses` in `Config.HorseStablesLogic.wildHorses` is off, or when the horse model has `canSaveWild = false`. In the shipped `cfg/configJobs.lua` the `unemployed` entry has `canSaveTame = false`, so only the listed jobs can save a tamed wild horse.

Selling can also be put on a cooldown with `cooldownForSellTameAgain` in `Config.HorseStablesLogic.wildHorses` (minutes, `0` disables it, the shipped value is `0`).

#### "You are not allowed use this job." when opening a stable

A stable with `isJobLimited = true` in its `npcSetting` stays usable outside `timeOpen` to `timeClose` only for players with `canColorHorse`. A stable with `jobLock` in its `baseSetting` opens only for the listed jobs.

#### Players get thrown off the horse during bonding training

Set `throwOff` in `Config.HorseLogic.bondingSystem`. It decides whether the horse throws the player off during bonding training.

### Integrating with other scripts

#### I need to remove a player's horse from another script (for example when they enter a store)

From a client script, send the horse or wagon away at once:

```lua
TriggerEvent("gum_stables:fleeHorseInstant")
TriggerEvent("gum_stables:fleeCartInstant")
```

From a server script (available since 2025-02-07), delete the player's horse entities:

```lua
TriggerEvent("gum_stables:deleteMyHorseServer", source)
```

#### I want to give a horse through Tebex

The 2026-06-15 update added `gum_stables:buyDataSrv` (server to server). The example ships in `serverCustom.lua`:

```lua
RegisterCommand("testCmd", function(source, args, rawCommand)
    print("testCmd executed")
    local horseData = {
        type = "horse", 
        cityLocation = 0, 
        model = "A_C_Horse_AmericanPaint_Greyovero",
        name = "Tebex horse", 
        age = 10, 
        sex = "Male", 
        breeding = "Yes", 
        currency = "Money",
        priceMoney = 0,
        priceGold = 0,
        bodyColor = {},
        tailManesColor = {},
        outfitStyle = 0,
        category = "American Paint horse",
        colorName = "Grey",
        defaultScale = 1.0,
        exp = 3000,
        courage = 9,
        friendly = 10,
        state = {{agility = 3,speed = 3,acceleration = 3}},
    }
    -- TriggerEvent("gum_stables:buyDataSrv", source, horseData, true) -- Server to server
end)
```

`category` and `colorName` must match a horse in `cfg/configHorses.lua`. The last argument (`true`) marks the horse as a Tebex horse. The player's horse limit (`horseCount` of the job) still applies.

When you combine the base stats with `state =`, the total for a horse stat must not go above 9.

### Reporting a bug

#### What to send so a bug can be fixed

Enable the debug version (`debug = true` in `Config.DebugAndBaseOption`), reproduce the problem, and send the print logs (a short video helps). The debug version traces the script's path through its functions and events. The developer also asks for your `cfg` and `sql` files, because the script has too many setting combinations to test without your exact configuration. Turn debug off again on a live server.

## Settings added in updates

Newest first. Each entry names the file and the table the line belongs in.

- **2026-11-12 (v1.2.7).** Replace `server.lua`, `fxmanifest.lua`, `client.lua`. Add the files `cfg/purchaseAccess.lua` and `horseCleanup.lua`. Add to a wagon in `cfg/configCarts.lua` or a horse in `cfg/configHorses.lua` (optional per entry, a missing key allows everyone):
  ```lua
  identifierForBuy = {},--Allowed steamIdentifiers; empty allows everyone.
  ```
  Add to `Config.Language` in `cfg/configLang.lua`:
  ```lua
  purchaseIdentifierDenied = "This horse or wagon is not available for your account.",
  ```
  Add to `Config.HorseLogic` in `cfg/config.lua`:
  ```lua
  ["deadHorseCleanup"] = {
      enabled = false, -- Remove expired dead horses once at resource startup (including old-age deaths).
      deleteAfterHours = 168, -- Real hours since death; 168 = 7 days. Removed on the next startup after expiry.
  },
  ```
- **2026-10-31 (v1.2.2).** In every job in `cfg/configJobs.lua`:
  ```lua
  canSaveTame = true, -- Set true for jobs allowed to save tamed wild horses
  ```
- **2026-10-30 (v1.2.1).** Replace `client.lua`, `server.lua`. Add to `cfg/configSrv.lua`:
  ```lua
  ConfigSrv.playerTransferRadius = 5.0
  ```
  Add to `Config.Language` in `cfg/configLang.lua`:
  ```lua
  [81] = {"Selected player is not close enough. Maximum distance: ", " m", 4},
  [165] = "Add user",
  ```
- **2026-09-10.** In `Config.HorseLogic.bondingSystem` (`cfg/config.lua`), first announced on 2026-08-14:
  ```lua
  throwOff = true, -- being thrown off a horse during bonding training
  ```
- **2026-08-27.** In `Config.HorseLogic.statusSetup`, under `brushItem = "Brush",`:
  ```lua
  brushMetadata = 10, -- false or num
  ```
- **2026-08-05.** Public stables can be locked to jobs. In the `baseSetting` of a stable in `Config.SpotsForStable` (`cfg/configCoords.lua`):
  ```lua
  jobLock = {
      ["HorseTrainer"] = true,
      ["jobName2"] = true,
  },
  ```
  Company stables in `Config.HorseCompany` accept several jobs:
  ```lua
  Job = {"HorseTrainer","HorseTrainer2"},
  ```
- **2026-07-12.** `["cityCallBlock"]` with `enabled` and `bannedTowns`, in `Config.HorseLogic` and `Config.CartLogic`. Language lines:
  ```lua
  [78] = {"You cant summon horse in this area.", 2},
  [79] = {"You cant summon cart in this area.", 2},
  ```
  Same day, in `Config.CartLogic.statusSetup` (optional, skip it if you do not need it):
  ```lua
  blockStorageWhileDamaged = true, -- If you have enabled, you cant open storage while cart is damaged
  ```
  ```lua
  [80] = {"You cant open storage while cart is damaged.", 2},
  ```
- **2026-06-15.** Per-horse lifespan in `cfg/configHorses.lua`. Without it, `maxAge` from `Config.HorseLogic.ageingSetup` applies:
  ```lua
  maxAge = 20,--Max age for horse (nil or 0 is disabled)
  ```
  Same update: event `gum_stables:buyDataSrv` for Tebex.
- **2026-05-13.** In `Config.HorseStablesLogic`:
  ```lua
  wheelChanges = false, -- This setting in the horse components and horse coloring changes the interaction wheels into classic numeric inputs with arrows.
  ```
  In `Config.Language`:
  ```lua
  [500] = "Scale",
  [501] = "Palette",
  [502] = "Color 1",
  [503] = "Color 2",
  [504] = "Color 3",
  [505] = "Style",
  ```
- **2026-04-20.** New database column `is_tebex` in `horses`, added by the script on start. Whether these horses age is set by `tebexHorsesAge` in `Config.HorseLogic.ageingSetup` (`false` by default).
- **2026-04-14, corrected 2026-04-17.** In `ConfigSrv.webhookLanguage` (`cfg/configSrv.lua`, not `cfg/configLang.lua`):
  ```lua
  [103] = "The horse died of old age.",
  [104] = "The horse died due to player error.",
  ```
- **2026-04-04.** In `cfg/config.lua`, top level:
  ```lua
  Config.BreederStoreAgeing = false-- Horses increase ages in breeder store if u enable that
  ```
- **2026-03-20.** In `Config.HorseStablesLogic.mating`. Works on a whistled horse, by item or command:
  ```lua
  removeMatingFromHorse = "BreederCut", -- If you have enabled, you can remove mating state from horse after mating. (nil is disabled)
  removeMatingFromHorseCmd = "matingremove", -- Command for remove mating state from horse (if you have enabled removeMatingFromHorse)
  ```
  In `Config.Language`:
  ```lua
  [350] = {'Do you want remove breeding state from horse?', 'Yes', 'No'},
  ```
- **2026-03-20.** In `Config.DebugAndBaseOption` (`cfg/config.lua`):
  ```lua
  -- /checkinv horse 142 or /checkinv cart 142
  -- this allow admins look to inventory 
  checkInventory = "checkinv",
  ["checkInventoryGroup"] = {
      "admin",
      "mod"
  },
  ```
- **2026-03-05.** In `cfg/configJobs.lua`, for every job that may transfer:
  ```lua
  canTransferToPlayer = true, -- Can transfer horse to player
  ```
- **2026-02-18.** New image `html/images/bordelStyle.png`. Slider styles replaced in `index.css`.
- **2026-01-11.** In the `npcSetting` of a stable in `Config.SpotsForStable`:
  ```lua
  isJobLimited = true,
  ```
- **2025-12-27.** In `Config.HorseStablesLogic`:
  ```lua
  ["lightSetting"] = {
      enabled = true, -- If you have enabled, you can set light settings for horses in stables at night time
      intensity = 0.4,
      range = 12.0,
      radius = 1.6,
      lightCount = 12,
  },
  ```
- **2025-12-27.** In `ConfigSrv.webhookLanguage`, for the horse coloring Discord log:
  ```lua
  [102] = "Change horse color",
  ```
- **2025-12-03.** Addon horses. Update `fxmanifest.lua` and the `stream` folder. In `cfg/configHorses.lua` the addon horses start at `["Lusitanos"]`.
- **2025-11-15.** New image `html/images/energy.png`. In `Config.HorseLogic.statusSetup.metabolism`:
  ```lua
  ["energySystem"] = {
      energyLostModifier = 0.15, -- Value of energy loss while sprinting
      minMaxEnergyCalculation = 10, -- Minimum stamina effect threshold at 10% energy

      -- Stamina calculation is based on horse level + current energy.
      staminaRechargeOrDrain = {0.05, 0.3}, -- {min, max} stamina gain/loss based on energy
      staminaRechargerBase = 0.8, -- Base stamina regen at 0 energy; fully rested gives ~1.1 regen
  },
  ```
- **2025-11-15.** In the `baseSetting` of a stable in `Config.SpotsForStable`, next to `quickPick`:
  ```lua
  quickPickCub = {-369.262939453125, 782.282470703125, 115.18479919433594, 4.22049474716186}, -- Quick pick coords for cubs
  ```
  In the jobs in `cfg/configJobs.lua`:
  ```lua
  canPickSmallHorseForTrain = true,
  ```
- **2025-11-09.** In `Config.HorseLogic.statusSetup`, under `horseKick`:
  ```lua
  horseKickYoung = false,
  ```
- **2025-11-04.** In `Config.HorseLogic.experienceSystem.experienceGain`, under `whileSwitchSideLongingSpamProtect`:
  ```lua
  whileLongingRun = 0.3,
  whileMirroringRun = 0.05,
  whileMirroringCrounch = 10,
  whileMirroringJump = 10,
  whileMirroringDance = 20,
  ```
  In the `baseSetting` of a stable in `Config.SpotsForStable` (leave it out or set it to `nil` if you do not want quick pick):
  ```lua
  quickPick = {-366.29075927734375,782.3577368164062,115.1771240234375,359.9140625}, -- Quick pick coords for horse from stables
  ```
  In `Config.Language`:
  ```lua
  trainingMirroring = "Mirroring",
  promptQuickNext = "Next",
  promptQuickBack = "Back",
  promptQuickPick = "Pick",
  ```
- **2025-09-12 and 2025-09-13.** Grazing. In `Config.HorseLogic.statusSetup.metabolism`, above `["gainWhileEat"]`, add `["grazeBlockLocations"]` (a list of `{name, coords, radius}` entries; empty is disabled). In `Config.Language` add `[76]` and `[77]` and replace `[27]`:
  ```lua
  [76] = {"Your horse cannot graze in cities. Move farther away from the city and try again.", 2},
  [27] = {"You are not near a hay pile. The horse will try to find grass nearby to eat.", 2},
  [77] = {"Your horse is not hungry.", 2},
  ```
- **2025-08-10.** In `Config.Language`:
  ```lua
  [164] = "Skin Texture",
  ```
- **2025-07-19.** `protectColoringByVip()` in `clientCustom.lua`. Required even if unused.
- **2025-06-10.** Company stables in `Config.HorseCompany`: `["enchantedMating"]` with `enabled` and `slots` (each slot is X, Y, Z, Radius).
- **2025-06-06.** Horseshoe wear, in `Config.HorseStablesLogic.shoes`:
  ```lua
  degradeWhileRide = 0, -- If enabled, shoes degrade while riding the horse (0 = Disabled / Value is % chance, e.g., 1.2% or 0.2%)  
  degradeWhileLead = 0, -- If enabled, shoes degrade while leading the horse (0 = Disabled / Value is % chance, e.g., 1.2% or 0.2%)  
  degradeEverySecond = 10, -- Every 10 seconds the system attempts to degrade the shoe (if enabled)
  ```
- **2025-06-06.** Transfer notifications, in `Config.Language`:
  ```lua
  [72] = {"You've received a new cart to the stables.", 3},
  [73] = {"You give cart to person", 3},
  [74] = {"You've recevied a new horse to the stables.", 3},
  [75] = {"You give horse to person", 3},
  ```
- **2025-05-18.** `Config.propsAttach` in `cfg/full_database.lua`: each entry takes the skeleton part as a last value (for example `"skel_neck5"`). The changelog marks this as requiring an update of the file.
- **2025-05-14.** Per horse in `cfg/configHorses.lua`, plus one line in `Config.Language`:
  ```lua
  seeIfJobBlock = true, --You'll see the horse, but you won't be able to buy it because of the job.
  ```
  ```lua
  [70] = {"This horse cannot be purchased in the shop.", 3},
  ```
- **2025-04-01.** Per horse in `cfg/configHorses.lua`: `statsModifierPerLvl`, health and stamina per horse level, values 1 to 9. Without it the original stats are used.
- **2025-03-30.** `isVip` for horses and wagons. New files `serverCustom.lua` and `clientCustom.lua`, and an updated `fxmanifest.lua`.
- **2025-03-07.** In `Config.HorseStablesLogic.wildHorses`:
  ```lua
  cooldownForSellTameAgain = 30, -- min for selling a tamed horse again (0 disables this)
  ```
- **2025-03-03.** In the jobs in `cfg/configJobs.lua`, plus one line in `Config.Language`:
  ```lua
  canSellTame = true, -- If can sell wild horses  
  canBuyCubs = true, -- Can buy cubs horses  
  ```
  ```lua
  [68] = {"You cant sell wild horse to stables.", 3},
  ```
  Same day: `ConfigSrv.webhookLanguage` lines `[98]` to `[101]` (see the foal entry above).
- **2025-02-07.** Event `gum_stables:deleteMyHorseServer`.
- **2025-01-23.** Required change to `["drinkProps"]` (`front` and `back` positions per trough).
- **2025-01-15 and 2025-01-16.** Saddle stands. In `Config.HorseLogic.transferComponents`:
  ```lua
  ["saddleStands"] = {-- for save saddles on saddle-stand  ["ModelName"] = "itemId",
      ["p_saddlestand01x"] = "saddleStand_1",
      ["p_saddlestand02x"] = "saddleStand_2",
      ["p_saddlestand03x"] = "saddleStand_3",
  },
  ```
  In `Config.Language`:
  ```lua
  saveSaddleStand = "Save saddle",
  takeSaddleStand = "Take saddle",
  pickSaddleStand = "Pick saddle",
  [348] = {'Do you want place saddle stand?', 'Yes', 'No'},
  [349] = {'Accept', 'Name for saddle on saddle stand?'},
  [219] = {"Stable stand : ", " Empty"},
  ```
  The three `saddleStand_` items also have to exist in your inventory.
