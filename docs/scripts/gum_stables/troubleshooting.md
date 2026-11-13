# GUM Stables: troubleshooting

This page lists the `gum_stables` problems that support tickets and the changelog show a fix for, plus the config lines, translation lines, and files added in updates.

## Check these first

1. **Old `horses`, `horses_breeding`, and `wagons` tables.** If these tables are left over from another stables script, drop them and restart `gum_stables`. The script creates its own tables. This resolved every database error ticket.
2. **gumApi is installed, starts before `gum_stables`.** On gumApi versions older than the 2026-10-20 update, the framework also has to be set in the gumApi config: a wrong or unset framework there produces nil value errors in `gum_stables`. Newer gumApi versions need no framework setting.
3. **Your config files have the lines added by the update you installed.** The script files are replaced on update, but your `cfg` files are yours, so new keys have to be added by hand. A missing key is the usual reason a feature stops working right after an update. See [Settings added in updates](#settings-added-in-updates).
4. **You can see the gumApi download.** It is only visible with the customer role in the GUM Scripts Discord.

## Problems and fixes

### Installation and database

#### The script has no SQL file

There is none to import. `gum_stables` creates its tables automatically when it starts. Install gumApi first, remove any old `horses`, `horses_breeding`, and `wagons` tables, then start `gum_stables`.

#### The console shows "gum_stables was unable to execute a query" with "Unknown column"

Example from a ticket:

```
[script:oxmysql] Error: gum_stables was unable to execute a query!
[script:oxmysql] Query: ALTER TABLE `horses_breeding` ADD COLUMN IF NOT EXISTS `reward_outfit` LONGTEXT DEFAULT NULL AFTER `reward_id`;
[script:oxmysql] Unknown column 'reward_id' in 'horses_breeding'
```

Cause: the database already has `horses`, `horses_breeding`, or `wagons` tables from another stables script, and their columns do not match what `gum_stables` expects.

Fix: delete the old `horses`, `horses_breeding`, and `wagons` tables, then restart `gum_stables`. It creates all the tables it needs. Dropping a table removes the horses and wagons stored in it, so do this on a fresh install, or back the tables up first.

#### Buying a wagon or horse gives a database error

Same cause and same fix as above: remove the old `horses` and `wagons` tables and restart the script.

If your server runs a newer MariaDB version, also update to the 2026-01-11 release or later. From that release the server creates certain values in SQL as a fallback, which fixed purchases of horses and carriages failing on newer MariaDB.

#### "You lack the required entitlement to use gum_stables" and "Couldn't start resource gum_stables"

Restart the complete server so the license access reloads.

#### I cannot find the gumApi download

The gumApi download is in a channel that only shows once you have the customer role in the GUM Scripts Discord. Open a role request ticket and give the Tebex transaction ID from your purchase email. If you left the Discord and rejoined, the role is gone and has to be requested again. A developer who works on your server gets access when the script owner opens a ticket and asks for a developer role for them.

### gumApi and start order

#### gum_stables does not start or does not work after install

`gum_stables` needs gumApi. Start gumApi before `gum_stables`, and set your framework in the gumApi config.

#### "attempt to call a nil value (field 'registerItem')"

Cause: the framework in the gumApi config is not set to the framework your server runs.

Fix: change the framework in the gumApi config, then restart.

### After an update

#### Players can no longer transfer or give a horse to another player

Cause: the 2026-03-05 update added a per-job permission for the "Transfer horse" button. If the key is missing from a job, that job cannot transfer.

Fix: add this line to every job in `ConfigJobs.lua` that should be able to transfer, then restart the script or the server:

```lua
canTransferToPlayer = true, -- Can transfer horse to player
```

Since version 1.2.1 (2026-10-30) the giver and the recipient also have to be close to each other. The maximum distance is set in `cfg/configSrv.lua`:

```lua
-- Maximum distance in meters between the giver and recipient for horse/wagon gifts.
ConfigSrv.playerTransferRadius = 5.0
```

#### Foals cannot be saved to the stable after breeding

Cause: the 2025-02-27 update needs four new lines in `Config.webhookLanguage`.

Fix: add them.

```lua
[98] = "Save breeded horse",
[99] = "Name : ",
[100] = "Model : ",
[101] = "Gender : ",
```

#### Horse death notifications do not reach Discord

The 2026-04-14 update added two lines for the horse death webhook. They belong in `WebhookLang`. If you put them in `ConfigLang`, move them:

```lua
[103] = "The horse died of old age.",
[104] = "The horse died due to player error.",
```

Also update client and server to the 2026-04-17 release or later, which fixed the messages not being sent.

#### Drinking from troughs stopped working after the 2025-01-23 update

That update changed troughs from one-sided to two-sided and requires a config change. Replace your `["drinkProps"]` section with the new format:

```lua
["drinkProps"] = {
    [GetHashKey("p_watertrough02x")] = {
        front = {0.0, -1.1, 0.0},
        back = {0.0, 1.1, 0.0},
    },
    [GetHashKey("p_watertrough01x")] = {
        front = {0.0, -1.1, 0.0},
        back = {0.0, 1.1, 0.0},
    },
}
```

#### Errors after the 2025-07-19 update

That update added `protectColoringByVip()` to `clientCustom.lua`. The function has to exist even if you do not use VIP coloring:

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
- The new file `cfg/purchaseAccess.lua` is added.
- The new lines are added to `cfg/configCarts.lua`, `cfg/configLang.lua`, and `cfg/config.lua` (listed under [Settings added in updates](#settings-added-in-updates)).

#### My custom slider styles broke after the 2026-02-18 update

That update replaced the slider styles and added the image `html/images/bordelStyle.png`. The new CSS is at the bottom of `index.css`. If you use custom graphics, remove these parts from `index.css`: `.slider`, `.slider::-webkit-slider-thumb`, `.slider::-moz-range-thumb`.

### Horses and wagons

#### Horses or wagons do not spawn after login on VORP

With `respawnAfterLogin` enabled, horses and wagons did not spawn on the VORP framework. Fixed in the 2025-03-03 update (client and server).

#### A horse spawns unpainted, or components load in the wrong color

Color and component loading was reworked several times (2025-06-10, 2025-06-16, 2025-11-04). The 2025-11-04 update made component loading faster and more reliable, so a whistled horse is colored as set in the stables. Update client and server to that release or later.

#### Horses from faction (company) stables do not spawn

Fixed in the 2025-09-24 update (`client` and `clientTag`).

#### The camera gets stuck when leaving the stables or a player shop

Fixed in the 2025-08-19 client update.

#### The wheel or repair minigame stays open on screen

Fixed in the 2025-09-24 update.

#### Courage, friendliness, or experience drop on their own

Fixed in the 2025-12-10 and 2025-12-12 updates (client and server). The second one also fixed quick pick overwriting those values on your horse.

#### Age or EXP shows long decimals in the menu (for example 25.00000002)

Fixed across the 2026-01-05, 2026-01-28, and 2026-02-06 updates to the js files. Replace `index.js` with the current one.

#### Server error "horseColoring.horseColorPal"

Fixed in the 2026-06-22 server update.

#### A horse removed from `configHorses.lua` causes errors

Selling a horse that was removed from the config was fixed on 2024-11-18. Aging a horse that was removed from `configHorses.lua` is protected since version 1.2.6 (2026-11-11). Replace `server.lua`.

#### A horse bought through Tebex does not age

This is intended. The 2026-04-20 update added an `is_tebex` column to the database, and it prevents those horses from aging.

### Messages that come from a setting

These are not bugs. Each message below is shown because a config option is switched on or a job permission is off.

#### "You cant summon horse in this area." or "You cant summon cart in this area."

Whistling is blocked in the towns listed in `["cityCallBlock"]` (in `Config.HorseLogic` and `Config.CartLogic`). Set `enabled = false` or remove the town from `bannedTowns`.

#### "You cant open storage while cart is damaged."

`blockStorageWhileDamaged = true` in `Config.CartLogic.statusSetup` blocks wagon storage while the wagon is broken.

#### "Your horse cannot graze in cities."

The horse is inside one of the areas in `["grazeBlockLocations"]`. An empty list disables the block.

#### "This horse cannot be purchased in the shop."

The horse has `seeIfJobBlock = true`, so players see it in the shop but cannot buy it unless they have the job.

#### "This horse or wagon is not available for your account."

The horse or wagon has entries in `identifierForBuy`. An empty list allows everyone.

#### "You can't sell a wild horse to the stables." or "You need to wait before selling a wild horse again"

The first comes from the job permission `canSellTame`. The second comes from `cooldownForSellTameAgain` in `Config.HorseStablesLogic.wildHorses` (`0` disables it). Since version 1.2.2 saving a tamed wild horse is also checked against `Config.Jobs.canSaveTame`.

#### Some players cannot use a stable during certain hours

A stable with opening hours and `isJobLimited = true` stays usable during the closed hours only for players with `canColorHorse`.

#### Players get thrown off the horse during bonding training

Set `throwOff` in `Config.HorseLogic.bondingSystem`. It decides whether the horse throws the player off during bonding training.

### Integrating with other scripts

#### I need to remove a player's horse from another script (for example when they enter a store)

Since 2025-02-07 there is a server-side delete.

Client to server:

```lua
TriggerServerEvent("gum_stables:deleteMyHorseClient")
```

Server to server:

```lua
TriggerEvent("gum_stables:deleteMyHorseServer", source)
```

#### I want to give a horse through Tebex

The 2026-06-15 update added `gum_stables:buyData` (client to server) and `gum_stables:buyDataSrv` (server to server). The changelog example:

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
    -- TriggerEvent("gum_stables:buyData", horseData, true) -- Client to server
    -- TriggerEvent("gum_stables:buyDataSrv", source, horseData, true) -- Server to server
end)
```

When you combine the base stats with `state =`, the total for a horse stat must not go above 9.

### Reporting a bug

#### What to send so a bug can be fixed

Enable the debug version, reproduce the problem, and send the print logs (a short video helps). The debug version traces the script's path through its functions and events. The developer also asks for your `cfg` and `sql` files, because the script has too many setting combinations to test without your exact configuration. Turn debug off again on a live server.

## Settings added in updates

Newest first. Unless a file is named, the changelog only gives the config section.

- **2026-11-12 (v1.2.7).** Replace `server.lua`, `fxmanifest.lua`, `client.lua`. Add the file `cfg/purchaseAccess.lua`. Add to `cfg/configCarts.lua`:
  ```lua
  identifierForBuy = {},--Allowed steamIdentifiers; empty allows everyone.
  ```
  Add to `cfg/configLang.lua`:
  ```lua
  purchaseIdentifierDenied = "This horse or wagon is not available for your account.",
  ```
  Add to `cfg/config.lua`:
  ```lua
  ["deadHorseCleanup"] = {
          enabled = false, -- Remove expired dead horses once at resource startup (including old-age deaths).
          deleteAfterHours = 168, -- Real hours since death; 168 = 7 days. Removed on the next startup after expiry.
      },
  ```
- **2026-11-11 (v1.2.6).** Replace `server.lua`. No config change.
- **2026-10-31 (v1.2.2).** New job setting `Config.Jobs.canSaveTame`: whether a job can save tamed wild horses.
- **2026-10-30 (v1.2.1).** Replace `client.lua`, `server.lua`. Add to `cfg/configSrv.lua`:
  ```lua
  ConfigSrv.playerTransferRadius = 5.0
  ```
  Add to `cfg/configLang.lua`:
  ```lua
      [81] = {"Selected player is not close enough. Maximum distance: ", " m", 4},
      [165] = "Add user",
  ```
- **2026-09-10.** `Config.HorseLogic.bondingSystem.throwOff` (first announced on 2026-08-14 as `throwOff = true` under `bondingSystem` in `config.lua`).
- **2026-08-27.** Under `brushItem = "Brush",` add:
  ```lua
  brushMetadata = 10, -- false or num
  ```
- **2026-08-05.** Public stables can be locked to a job (in `baseSettings`), and company stables accept several jobs:
  ```lua
  jobLock = {
      ["HorseTrainer"] = true,
      ["jobName2"] = true,
  },
  ```
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
- **2026-06-15.** Per-horse lifespan. Without it the default age from the main config applies:
  ```lua
  maxAge = 20,--Max age for horse (nil is disabled)
  ```
  Same update: events `gum_stables:buyData` and `gum_stables:buyDataSrv` for Tebex.
- **2026-05-13.** In `Config.HorseStablesLogic`:
  ```lua
  wheelChanges = false, -- This setting in the horse components and horse coloring changes the interaction wheels into classic numeric inputs with arrows.
  ```
  In `Config.Lang`:
  ```lua
  [500] = "Scale",
  [501] = "Palette",
  [502] = "Color 1",
  [503] = "Color 2",
  [504] = "Color 3",
  [505] = "Style",
  ```
- **2026-04-20.** New database column `is_tebex`. It currently prevents those horses from aging.
- **2026-04-14, corrected 2026-04-17.** In `WebhookLang` (not `ConfigLang`):
  ```lua
  [103] = "The horse died of old age.",
  [104] = "The horse died due to player error.",
  ```
- **2026-04-04.** Config only:
  ```lua
  Config.BreederStoreAgeing = false-- Horses increase ages in breeder store if u enable that
  ```
- **2026-03-20.** In `Config.HorseStablesLogic.mating` (comments as reposted on 2026-05-13). Works on a whistled horse, by item or command:
  ```lua
  removeMatingFromHorse = "BreederCut", -- If enabled, you can remove the mating state from a horse after breeding. (nil disables it) (its item)
  removeMatingFromHorseCmd = "matingremove", -- Command to remove the mating state 
  ```
  ```lua
  [350] = {'Do you want remove breeding state from horse?', 'Yes', 'No'},
  ```
- **2026-03-20.** In `Config.lua`, section `Config.DebugAndBaseOption`:
  ```lua
  -- /checkinv horse 142 or /checkinv cart 142
  -- this allow admins look to inventory 
  checkInventory = "checkinv",
  ["checkInventoryGroup"] = {
      "admin",
      "mod"
  },
  ```
- **2026-03-05.** In `ConfigJobs.lua`, for every job:
  ```lua
  canTransferToPlayer = true, -- Can transfer horse to player
  ```
- **2026-02-18.** New image `html/images/bordelStyle.png`. Slider styles replaced in `index.css`.
- **2026-01-11.** Stable setting `isJobLimited = true`.
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
- **2025-12-27.** Webhook language, for the horse coloring Discord log:
  ```lua
  [102] = "Change horse color",
  ```
- **2025-12-03.** Addon horses. Update `fxmanifest` and the `stream` folder. In ConfigHorses the addon horses start at `["Lusitanos"]`.
- **2025-11-15.** New image `html/images/energy.png`. Under `Config.HorseLogic.statusSetup.metabolism`:
  ```lua
  ["energySystem"] = {
      energyLostModifier = 0.15, -- Value of energy loss while sprinting
      minMaxEnergyCalculation = 10, -- Minimum stamina effect threshold at 10% energy

      -- Stamina calculation is based on horse level + current energy.
      staminaRechargeOrDrain = {0.05, 0.3}, -- {min, max} stamina gain/loss based on energy
      staminaRechargerBase = 0.8, -- Base stamina regen at 0 energy; fully rested gives ~1.1 regen
  }
  ```
- **2025-11-15.** Under `quickPick` in ConfigCoords, and in the jobs config:
  ```lua
  quickPickCub = {-369.262939453125, 782.282470703125, 115.18479919433594, 4.22049474716186} -- Quick pick coords for cubs
  ```
  ```lua
  canPickSmallHorseForTrain = true,
  ```
- **2025-11-09.** In `Config.lua` under `"horseKick"`:
  ```lua
  horseKickYoung = false,
  ```
- **2025-11-04.** In `Config.lua`, under `whileSwitchSideLongingSpamProtect`:
  ```lua
  whileLongingRun = 0.3,
  whileMirroringRun = 0.05,
  whileMirroringCrounch = 10,
  whileMirroringJump = 10,
  whileMirroringDance = 20,
  ```
  In `ConfigCoords.lua`, section `Config.SpotsForStable.baseSetting` (leave it out or set it to `nil` if you do not want quick pick):
  ```lua
  quickPick = {-366.29075927734375,782.3577368164062,115.1771240234375,359.9140625}, -- Quick pick coords for horse from stables
  ```
  In `ConfigLang.lua`:
  ```lua
  trainingMirroring = "Mirroring",
  promptQuickNext = "Next",
  promptQuickBack = "Back",
  promptQuickPick = "Pick",
  ```
- **2025-09-12 and 2025-09-13.** Grazing. In `config.lua`, above `"gainWhileEat"`, add `["grazeBlockLocations"]` (a list of `{name, coords, radius}` entries; empty is disabled). In `Config.Lang` add `[76]` and `[77]` and replace `[27]`:
  ```lua
  [76] = {"Your horse cannot graze in cities. Move farther away from the city and try again.", 2},
  [27] = {"You are not near a hay pile. The horse will try to find grass nearby to eat.", 2},
  [77] = {"Your horse is not hungry.", 2},
  ```
- **2025-08-10.** Language file:
  ```lua
  [164] = "Skin Texture",
  ```
- **2025-07-19.** `protectColoringByVip()` in `clientCustom.lua`. Required even if unused.
- **2025-06-10.** Company stables, `["enchantedMating"]` with `enabled` and `slots` (each slot is X, Y, Z, Radius).
- **2025-06-06.** Horseshoe degrading:
  ```lua
  degradeWhileRide = 0, -- If enabled, shoes degrade while riding the horse (0 = Disabled / Value is % chance, e.g., 1.2% or 0.2%)  
  degradeWhileLead = 0, -- If enabled, shoes degrade while leading the horse (0 = Disabled / Value is % chance, e.g., 1.2% or 0.2%)  
  degradeEverySecond = 10, -- Every 10 seconds the system attempts to degrade the shoe (if enabled)
  ```
- **2025-06-06.** Transfer notifications:
  ```lua
  [72] = {"You've received a new cart to the stables.", 3},
  [73] = {"You give cart to person", 3},
  [74] = {"You've recevied a new horse to the stables.", 3},
  [75] = {"You give horse to person", 3},
  ```
- **2025-05-18.** `Config.propsAttach` in `full_database.lua`: each entry takes the skeleton part as a last value (for example `"skel_neck5"`). The changelog marks this as requiring an update of the file.
- **2025-05-14.** Per horse, plus one language line:
  ```lua
  seeIfJobBlock = true, --You’ll see the horse, but you won’t be able to buy it because of the job.
  ```
  ```lua
  [70] = {"This horse cannot be purchased in the shop.", 3},
  ```
- **2025-04-01.** `statsModifierPerLvl`: health and stamina per level, values 1 to 9. Without it the original stats are used.
- **2025-03-30.** `isVip` section for horses and carriages. New files `serverCustom` and `clientCustom`, and an updated `fxmanifest`.
- **2025-03-07.** In `Config.HorseStablesLogic.wildHorses`, plus one language line:
  ```lua
  cooldownForSellTameAgain = 30, -- min for selling a tamed horse again (0 disables this)
  ```
  ```lua
  [69] = {"You need to wait before selling a wild horse again", 3},
  ```
- **2025-03-03.** In configJobs and configLanguage:
  ```lua
  canSellTame = true, -- If can sell wild horses  
  canBuyCubs = true, -- Can buy cubs horses  
  ```
  ```lua
  [68] = {"You can't sell a wild horse to the stables.", 3},
  ```
  Same day: `Config.webhookLanguage` lines `[98]` to `[101]` (see the foal entry above).
- **2025-02-07.** Events `gum_stables:deleteMyHorseClient` and `gum_stables:deleteMyHorseServer`.
- **2025-01-23.** Required change to `["drinkProps"]` (`front` and `back` positions per trough).
- **2025-01-15 and 2025-01-16.** Saddle stands. In `Config.HorseLogic.transferComponents`:
  ```lua
  ["saddleStands"] = {-- for save saddles on saddle-stand  ["ModelName"] = "itemId",
      ["p_saddlestand01x"] = "saddleStand_1",
      ["p_saddlestand02x"] = "saddleStand_2",
      ["p_saddlestand03x"] = "saddleStand_3",
  },
  ```
  Language:
  ```lua
  saveSaddleStand = "Save saddle",
  takeSaddleStand = "Take saddle",
  pickSaddleStand = "Pick saddle",
  [348] = {'Do you want place saddle stand?', 'Yes', 'No'},
  [349] = {'Accept', 'Name for saddle on saddle stand?'},
  [219] = {"Stable stand : ", " Empty"},
  ```

*Built from the changelog and resolved support tickets. For anything not covered here, open a ticket in the GUM Scripts Discord.*
