---
icon: clipboard-list
---

# GUM MDT

GUM MDT is a records system for law and government jobs on RedM: person records, cases, prisoners, documents, and wanted persons. It ships together with a bounty hunt system, and its updates are published under the name `gum_mdt_bountyhunt`.

## At a glance

- Video: [GUM MDT on YouTube](https://youtu.be/oEA3y0GuMc8)
- Store: [MDT + Bounty hunt on Tebex](https://gum-scripts.tebex.io/package/4804557)
- Frameworks: the release announcement lists VORP and RedEM. The changelog also contains fixes specific to RSG Core.

## What it does

### Access control

- Each MDT can be locked to specific jobs in your configuration.
- An MDT is opened either at set coordinates or by using an item in the inventory.

### Citizen records

- Citizens are sorted into categories: A, B, C, D, and Deceased. A person can be moved into Deceased.
- The list shows each person's ID and lets you delete records.
- Fingerprints have their own interface, and the script generates a unique fingerprint pattern for every player.
- Fines: you create a list of fines with amounts, then type `@` in the fines interface to get autocomplete from that list.
- An "Additional Information" section holds unlimited notes, for example "Identification Details" or "Violation Records". Typing `@` there links existing cases to the person.
- Bounties can be added to and removed from a person.
- Changes must be saved. Data is shared between players.

### Cases and reports

- Cases are sorted into categories. The categories you create are used in the MDT automatically.
- Each case has a Case Summary as its main description, plus an Additional Information section with unlimited notes.
- In summaries and notes, `@` links people and `#` links evidence.
- Evidence can be items or photos.
- Category permissions let you choose which jobs can access what (police, sheriff, government, and so on).
- A case can be locked. A locked case cannot be edited or deleted, and only selected ranks can unarchive it.
- Highlighted `@` and `#` references can be double-clicked to jump to the related entry, evidence image, or person profile.
- The MDT suggests entries as you type to speed up the work.

### Documents

- A Document Archive tab with document creation.
- Text color and font selection in created documents.
- Players open their own ID cards and documents with a command, and can show them to a nearby player.

### Wanted persons and prisoners

- A wanted persons registry linked to the bounty boards.
- A prisoners category. Prisoners can also be removed from the wanted registry.

### Search and audit trail

- Search across all notes. Searching "robbery" returns every case that mentions robbery.
- Each entry records who created it and who last edited it, with timestamps.

### Bounty hunting

- A bounty hunting system with configurable targets, spawn locations, delivery points, and cooldowns.
- Players capture targets and deliver them. The script checks deliveries to prevent invalid ones.
- The server tracks bounty availability and delivery progress, and can restore a carried target if the carrier disconnects.
- Bounty NPCs react to nearby players, line of sight, and gunshots. They investigate sounds, search for threats, and return to their patrol area.
- Map indicators for active bounties, with configurable visibility and distance.
- Optional debugging tools for administrators to inspect NPC perception and bounty behavior.

The developer recommends watching the video, because the script is large and the video explains each part.

## Requirements

- **gumApi**. Install it first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).
- **oxmysql**.

Both are declared as dependencies in `fxmanifest.lua`, so the resource does not start without them.

Optional:

- **gum_jail**: when it is started, the MDT prisoner list shows its `jaildata` table. See [GUM Jail](gum_jail.md).
- **gum_housing**: used by `Config.fineToJobHouse`, see below.

Keep the folder name `gum_mdt`. The default config points to images with `nui://gum_mdt/...` paths.

## Install

1. Put the `gum_mdt` folder in your resources and start it after `gumApi` and `oxmysql`.
2. You do not need to import any SQL. On start the script creates its tables if they do not exist:

| Table | Holds |
| --- | --- |
| `mdtdatabase` | Person records and cases, with the MDT they belong to. |
| `mdtdatabasefines` | Fines given to characters and whether they are paid. |
| `mdtidcards` | ID cards issued to characters. |
| `mdtdocuments` | Documents created by players. |
| `mdtfingers` | Fingerprints. |
| `mdtwanted` | Wanted persons and the NPC bounty posters. |
| `mdt_bounty_payouts` | Bounty rewards waiting to be paid out. |
| `gum_mdt_bounty_cooldowns` | Cooldowns of NPC bounty posters. |

3. Add an item for every MDT in `Config.MDTDatabase` that has an empty `coords` list. The item name is the key of the entry. With the default config these are `MDT_Book`, `PMDT_Book`, `DMDT_Book`, and `AMDT_Book`.
4. Replace the example job names in `config.lua` with your own job names.

If your database still has the tables of the old beta version (`mdt`, `mdt_comment`, `mdt_comment_police`, `mdt_doctor`), the script moves their rows into `mdtdatabase` on start and then drops the old tables. It also drops an old `mdt_wanted` table without moving it. Back up your database before the first start.

## Configuration

Everything is in `config.lua`.

### MDT books (`Config.MDTDatabase`)

Each entry is one MDT. The key is the name of the MDT and of its item.

| Key | What it does |
| --- | --- |
| `coords` | A list of `{x, y, z, range}` points. The MDT opens with a prompt when the player stands on the point. Leave it empty (`{}`) to open the MDT with the item. |
| `jobLock` | The jobs that can open it, as `{"Label", "job_name"}` pairs. An empty list lets everyone open it. |
| `archiveLockGrade` | The lowest job grade that can lock and unlock cases. `0` lets every job in `jobLock` do it. |
| `archiveStorageLock` | A list of `{x, y, z, radius}` points. The storage of a case only opens inside one of them. An empty list lets it open anywhere. |
| `isShared` | `false` keeps the records of this MDT to itself. A list of MDT names shares records with the other MDTs that are also shared. Put the same list on every MDT in the group. |
| `wantedList` | Shows the wanted tab. |
| `jailList` | Shows the prisoner tab. |
| `documentList` | Shows the document tab. |
| `fineList` | The preset fines offered by the `@` autocomplete, written as `'Reason, 10$'`. |
| `type` | The layout of the MDT. The default entries all use `"personBookComment"`. |
| `htmlTexts` | The labels of the case tab for this MDT. |

`Config.MDT_Book`, `Config.PMDT_Book`, and `Config.DMDT_Book` are only used to move data from the old beta tables.

### Fines

- `Config.FinesList`: the places where players pay their fines. Each has `coords`, a `blip` sprite (`0` for none), and a `blipName`.
- `Config.fineToJobHouse` (default `true`): when a fine is paid, the script triggers `gum_housing:addMoneyToFraction` with the amount and the `jobLock` of the MDT the fine came from. This only does something if you run `gum_housing`.

### Documents and ID cards

`Config.documentSystem`:

| Key | Default | What it does |
| --- | --- | --- |
| `enabled` | `true` | Turns on the player command that opens their own cards and documents. |
| `openDocumentList` | `"documents"` | The name of that command. |
| `checkDocuments` | `true` | Turns on the command that lets a job check the documents of the nearest player. |
| `checkDocumentCMD` | `"checkDocument"` | The name of that command. |
| `checkDocumentsJob` | `{"Sheriff_VL"}` | The jobs that can use it. |

`Config.Documents` lists the document templates. Each has a `background` image, a `name`, and the `jobs` that get it offered. The default names are in Czech, so rename them. The background images are in `html/documents/documents/`.

### Bounty boards and NPC bounties

| Key | Default | What it does |
| --- | --- | --- |
| `Config.DisableBounty` | `false` | `true` turns the bounty system off. |
| `Config.Bounty_Board` | 4 towns | The boards. Each has `coords`, a `blip` and `blipName`, the delivery NPC model (`rewardsNpc`), and the delivery position (`rewards`). |
| `Config.BountyDifficulty` | `easy`, `medium`, `hard` | Per tier: `price` range, `npc` count range, `weapons`, `npcHealth` (`0` keeps the game default), `generateChance`, `onlyLiveChance` (percent of posters that pay only for a live target), and `priceForLive` (reward multiplier for delivering alive on a dead or alive poster). |
| `Config.Spawn_Coords` | 14 points | Where bounty targets can spawn, as `{x, y, z, heading}`. |
| `Config.Bounty_Image_Npc` | 15 entries | The poster images and the NPC models behind them (`model_1` is the target, `model_2` the guards). |
| `Config.Max_Npc_Bounty` | `10` | The most NPC posters on the boards at once. |
| `Config.Refill_Bounty` | `1` | Minutes between refills. |
| `Config.HowMuchRefill` | `2` | Posters added per refill. |
| `Config.BountyTimeoutIfCancel` | `{360, 2400}` | Minimum and maximum mission time in seconds, scaled by the distance to the target. |
| `Config.FirstnameMale`, `Config.FirstnameFemale`, `Config.Lastname` | name lists | Names used for generated targets. |

`Config.BountyWorld` controls the shared encounter:

- `routingBucket` (`0`): the routing bucket bounties run in.
- `spawnDistance` (`150.0`): how close a player must be before the target spawns.
- `deliveryDistance` (`4.0`): how close the player and the target must be to the delivery NPC.
- `carrierDistance` (`7.0`): how close a player must be to the target to count as the one carrying it.
- `cooldownSeconds` (`3600`): how long a timed-out bounty stays unavailable.
- `deliveryTimeoutSeconds` (`3600`): the time to deliver once the target is captured.
- `recoverCarrierDisconnect` (`true`): if the carrier disconnects, the target is restored as a dead body at its last position.

### Bounty NPC behavior

- `Config.BountyPerception`: sight, hearing, and search. The main keys are `enabled`, `seeingRange` (`75.0`), `fieldOfView` (`110.0`), `recognitionSeconds` (`1.2`), `crouchSightMultiplier`, `nightSightMultiplier`, the noise ranges (`walkNoiseRange`, `runNoiseRange`, `crouchNoiseRange`, `horseWalkRange`, `horseRunRange`, `gunshotRange`), `allyAlertRange`, `loseSightSeconds`, `searchSeconds`, `wanderEnabled` and `wanderRadius`, and `returnHome`. The keys that start with `debug` are for testing.
- `Config.BountyPerception.enabled = false` switches back to the older behavior, where NPCs attack inside `Config.NPCAttackRange` (`80.0`) when `Config.NPCAttackOnRange` is `true`.
- `Config.BountyCombat`: `useCover`, `blindFire`, `movement` (0 stationary to 3 very aggressive), `range`, and `advanceWithoutCover`.
- `Config.BountyIndicators`: the awareness icons above bounty NPCs. `enabled`, `iconScale`, `scaleDistance`, `showIdle`, `maxDistance` (`50.0`), `maxVisible` (`8`), and `combatVisibleMs`.

The folder also contains `BOUNTIES.md`, the developer's own notes on how the bounty NPCs behave.

### Language

`Config.Language` holds every notification, prompt, and label. Numbered entries such as `[17]` are notifications in the form `{title, text, seconds}`. `Config.Language.image` sets the notification image.

If you kept an older `config.lua` through updates, check that these lines are in `Config.Language`. They were added by updates:

```lua
    [17] = {"MDT", "No player is near you", 2},
    [21] = {"MDT", "Person does exist. First u need save them.", 2},
    [112] = {'Give or remove documents?', 'Give', 'Remove'},
    [113] = {'Player ID', 'Accept'},
    buttonBountyBoardPrompt = "Bounty Board",
```

## Commands

| Command | Who | What it does |
| --- | --- | --- |
| `/documents` | Everyone | Opens your own ID cards and documents. The name comes from `Config.documentSystem.openDocumentList`. |
| `/checkDocument` | Jobs in `checkDocumentsJob` | Opens the cards and documents of the nearest player. The name comes from `Config.documentSystem.checkDocumentCMD`. |
| `/mdt_bounty_debug [on/off]` | Everyone | Toggles the bounty NPC debug display on your own client. |
| `mdt_bounty_test <player ID> [dead/alive]` | Server console, or players with the ACE `command.mdt_bounty_test` | Spawns a test bounty target next to that player. It lasts five minutes and pays nothing. |

## Customers also ran into these

### A fine cannot be added to a new person

This is intended. Create and save the person first, then add the fine.

### Anyone can remove a wanted entry from the Wanted Board

Removing a wanted entry did not check the player's job. This was fixed on 2026-08-20. The server now checks the `jobLock` of the MDT before it removes the entry. Update to that version or later.

### Fingerprints are saved several times for the same person

Generating fingerprints saved duplicates, one for each time the MDT had been opened. This was fixed on 2026-07-03. Update to that version or later.

### Fingerprints cannot be created on RSG Core

This was fixed on 2026-08-13 in the client and server files. Update to that version or later.

### ID cards do not load

Loading of ID cards was fixed on 2026-08-13. Update to that version or later.

### On RSG, fines are not created when creating a new entry

This was fixed on 2026-08-20 in the client files. Update to that version or later.
