---
icon: handcuffs
---

# GUM Jail

GUM Jail is a jail script for RedM. It covers handcuffing, dragging, jailing, wagon transport, escapes, and hanging.

## At a glance

- Video: [Jail V3 Update](https://www.youtube.com/watch?v=s7mpxPsvFRo)

## What it does

### Handling players

- Handcuff players.
- Lead handcuffed players.
- Put players into a prisoner outfit.
- Load players into the prison wagon and take them out again.
- Hang players.

### Jailing

- Jailing and unjailing are available to specific jobs.
- The jail area is a fixed zone. A prisoner who leaves it gets 10 seconds to return, then is put back and gets extra time.
- Prisoners can work inside the jail. You can add more work spots if you have looping animations for them.
- Prisoners can fight in the ring to shorten their sentence.
- A job can also sentence a player to community work in a town, without sending them to jail.

### Escapes and robbery

- Prisoners can escape through a quest: they collect materials around the jail and leave by boat. The quest is customizable.
- NPC robbery is included and can be turned on in the config.

### Storage

- Sentences and work sentences are saved in SQL, so they survive a relog and a server restart.

## Requirements

- **gumApi**. Install it first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).
- **oxmysql**.
- **gum_target** when `Config.EnchantedTargetSystem` is `true` (the default). The script then registers its interactions as targets through `gum_target`.

## Install

1. Put the `gum_jail` folder in your resources and start it after `gumApi` and `oxmysql`.
2. You do not need to import any SQL. On start the script creates three tables if they do not exist:

| Table | Holds |
| --- | --- |
| `jaildata` | Active sentences: character, name, start and end time, remaining time. |
| `jailwork` | Active community work sentences: character, town, remaining work count. |
| `robbery` | Robbery name and time. |

3. Set your law jobs in `Config.JailJobsManage`.

## Configuration

The config is split into three files in the `cfg` folder.

### `cfg/baseSetting.lua`

| Key | Default | What it does |
| --- | --- | --- |
| `Config.JailJobsManage` | 3 sheriff jobs | The jobs that can jail, unjail, handcuff, hang, and give community work. Replace the example names with your own job names. |
| `Config.EnchantedTargetSystem` | `true` | Use `gum_target` targets for interactions. |
| `Config.JailPenalizationIfTryEscape` | `600` | Seconds added to the sentence when a prisoner leaves the jail zone or fails an escape. |
| `Config.ChanceOnEscapeWhileDay` | `25` | Percent chance that an escape by boat succeeds during the day. |
| `Config.ChanceOnEscapeWhileNight` | `50` | Percent chance at night (20:00 to 06:00 game time). |
| `Config.FightReduction` | `20, 35, 55, 75, 100` | Seconds taken off the sentence for each won fight round. The number of entries is the number of rounds. |
| `Config.discordWebhook` | `enable = false` | Webhook for NPC robbery logs: `enable`, `url`, `color`. |
| `Config.Debug` | `true` | Debug switch. Set it to `false` on a live server. |

#### Commands (`Config.Commands`)

| Command | Default name | Who | What it does |
| --- | --- | --- | --- |
| `jailWork` | `/jailwork <ID> <Count>` | Jobs in `Config.JailJobsManage` | Gives the player community work in the town you are standing in. `Count` is the number of work spots to finish. You must be inside a town from `Config.cityWorkPosition`. |
| `jail` | `/jail <ID> <Time>` | Jobs in `Config.JailJobsManage` | Sends the player to jail. |

- `jailWork.enabled` turns the first command on or off.
- `jail.enabledJob` turns the `/jail` command on for the jobs. `jail.enabledAdmin` and `jail.adminRoles` are the switch and the group list for admins.
- `command`, `description`, and `params` set the command name and its chat suggestion.

The script counts sentences in seconds. Jailing through the sheriff NPC asks for minutes and converts them. The `/jail` command passes its `Time` value on unchanged.

#### NPC robbery

| Key | Default | What it does |
| --- | --- | --- |
| `Config.NPCRob` | `false` | `true` lets players rob an NPC by aiming a weapon at them and walking up. |
| `Config.NPCReward` | `{1.10, 3.99}` | Minimum and maximum money from one robbery. |
| `Config.NPCLoot` | `false` | `true` lets players loot dead NPCs. |
| `Config.NPCDrops` | 10 entries | Items a looted NPC can give: `itemId`, `label`, `count`, `chance`. The item names must exist in your inventory items. |
| `Config.NPCRobDelayAndLoot` | `2` | Delay before robbed NPCs reset. |

### `cfg/workSetting.lua`

- `Config.jobInWork`: the work spots inside the jail. Each has a `name`, `coords`, an `animation` (dictionary and name), a `workTime` (seconds the work takes), a `timedown` (seconds taken off the sentence), and an optional prop (`propBone`, `coordProps`).
- `Config.cityWorkPosition`: the towns for community work. The defaults are `BlackWater`, `Valentine`, `Tumbleweed`, `SaintDenis`, `Rhodes`, and `Armadillo`. Each town has:
  - `cityCenter`: x, y, z, and the radius of the town.
  - `spotForSpawn`: where the player is put back if they leave that radius. Every town needs it.
  - `workCoords`: the list of work spots.
  - `workTime`: seconds for one spot.
  - `workSpamReset`: reset time for a used spot.
  - `wearClothe`: `true` puts the player in the prisoner outfit.
- `Config.PenalizationCounter`: the remaining work is multiplied by this number when the player leaves the town (default `2`).

### `cfg/coordSetting.lua`

All positions for the jail itself.

- `Config.JailPolyzone`: the corner points of the jail zone.
- `Config.EnterOrEscapeFromJailCoords`: the entry point, the guard NPC positions, and where a new prisoner is placed.
- `Config.PoliceNPCs`: guard NPC positions.
- `Config.hangPosition`: gallows, by town. Each has a `leverPosition` and a `hangPosition`.
- `Config.CartSpawnCoords`: where sheriffs take out and return the prison wagon (`modelForSpawn`, `spotForTake`, `cartCoords`).
- `Config.BoatSpawnCoords`: where sheriffs take out and return a boat (`modelForSpawn`, `spotForTake`, `boatCoords`).
- `Config.EscapeMission`: the materials for the escape quest. Each material has a prop (`propName`), the amount needed (`countNeed`), and the places it can appear (`whereCanFind`).
- `Config.RingPropPosition` and `Config.FansNPCs`: the fighting ring and its spectators.

## With GUM MDT

If you run [GUM MDT](gum_mdt.md), its prisoner list reads the `jaildata` table. MDT checks by itself whether `gum_jail` is started, so the two scripts can run with or without each other.
