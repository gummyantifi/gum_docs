---
icon: dice
---

# GUM Blackjack

GUM Blackjack is a multiplayer blackjack game for RedM. Players sit at configurable tables and bet against an automatic dealer.

## At a glance

- Video: [GUM Blackjack](https://youtu.be/qjodclN-imo)
- Store: [GUM Scripts on Tebex](https://gum-scripts.tebex.io/category/redm)
- Bundle with GUM Poker: [Poker + Blackjack](https://gum-scripts.tebex.io/package/7786138)

## What it does

### Multiplayer tables

- Several players can play at the same table.
- You can run multiple independent blackjack tables.
- Cards, bets, and game results are synchronized between players.
- The dealer plays and pays out automatically.

### Blackjack rules

- Players can Hit, Stand, Double Down, and Split.
- The payout for a natural blackjack is configurable. Ordinary wins pay 1 to 1.
- Dealer Soft 17 behaviour is configurable.
- Each table sets how many card decks it uses.
- The maximum number of split hands is configurable (2 to 4).
- Double After Split can be turned on.

### Betting

- Each table has its own minimum bet, maximum bet, bet step, and default bet.
- Wagers and payouts are handled automatically, in dollars from the player's cash.

### Presentation

- Character animations for play.
- Multiple camera views.
- A dealer NPC model per table.
- An interactive blackjack interface with a rules and guide page.
- Configurable table, chair, and card back models.
- Custom player seating arrangements.

### Other options

- Custom blackjack table locations.
- Configurable interaction keys.
- Adjustable game timers and action delays.
- Optional map blips.
- Optional Discord logging.

## Before you install

Install gumApi first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md). `fxmanifest.lua` lists `gumApi` as the only dependency.

## Install

1. Put the `gum_blackjack` folder in your resources.
2. Start it after gumApi:

```cfg
ensure gumApi
ensure gum_blackjack
```

There is no SQL file to import and no item to add. The script does not use a database. Open wagers and unpaid winnings are kept in the resource's own server KVP storage, so they survive a restart.

## Configuration

All editable files are in `configs/`.

| File | What it holds |
| --- | --- |
| `configs/config.lua` | Keys, rules, timing, camera, UI, blips, models, and the table list |
| `configs/configLanguage.lua` | All player-facing text, in `Config.Text` |
| `configs/configServer.lua` | Discord webhook, in `ConfigSrv.discordWebhook` (server only) |

### Rules: `Config.Rules`

These apply to every table.

| Key | Default | Meaning |
| --- | --- | --- |
| `naturalPayout` | `1.5` | Profit multiplier for a two-card 21. `1.5` is 3:2. The stake is returned as well. Allowed range is 0 to 10. |
| `hitSoft17` | `false` | `false`: the dealer stands on every 17. `true`: the dealer draws on soft 17. |
| `double` | `true` | Allow doubling the bet for exactly one more card. |
| `split` | `true` | Allow splitting two cards of the same rank. Split aces get one card each, and a split 21 pays as a normal win. |
| `maxHands` | `2` | Most hands one player can hold after splitting. Must be a whole number from 2 to 4. |
| `doubleAfterSplit` | `false` | Allow doubling a hand that came from a split. |

### Tables: `Config.Tables`

Each entry is one table. The default config ships five: Blackwater, Valentine, Tumbleweed, Saint Denis, and Rhodes.

| Key | Meaning |
| --- | --- |
| `name`, `description` | Text shown for the table. |
| `blip` | `true` shows a map blip for this table when `Config.Blips.enabled` is on. |
| `dealerModel` | Ped model of the dealer. |
| `position` | `{x, y, floor z, heading}`. This is the dealer spawn and the anchor for the table. |
| `spawnTable`, `spawnChairs` | Set to `false` to use a table or chairs that already exist on the map. Seat positions still come from the layout values. |
| `seats` | Number of player chairs (5 in the default tables). |
| `decks` | Number of 52-card decks in this table's shoe (6 by default). |
| `bettingSeconds` | Time players have to place the opening bet (15 by default). |
| `bets` | `minimum`, `maximum`, `step`, and `default`, in dollars. Defaults are 1, 50, 1, and 5. |
| `chairs` | Chair ring layout: `radiusX`, `radiusY`, `arc`, `edgeAngle`, `edgeRotation`, `seat1HeadingOffset`, `zOffset`. |

The script refuses to start if a table has too few decks for its seats and `maxHands`. The error reads `Increase decks for blackjack table <id>`.

### Keys: `Config.Prompts`

- `newPrompt` (default `true`) chooses between the two gumApi prompt systems.
- `key` (default `'E'`) is the key to sit down.
- `actions` maps each in-game action to a key. Defaults: `bet` left mouse, `hit` E, `stand` F, `double` R, `split` G, `leave` Backspace, `less` and `more` mouse wheel, `camera` C, `rules` Y.

`Config.KeyMapping` (`enabled = true`, `prefix = 'GUM Blackjack'`) registers the prompt keys in the RedM key binding settings under that prefix.

### Timing and distances: `Config.Interaction`

| Key | Default | Meaning |
| --- | --- | --- |
| `distance` | `1.2` | How close a player must be to a chair to sit, in metres. |
| `serverDistance` | `4.5` | Server-side distance check to the dealer. |
| `spawnDistance`, `despawnDistance` | `40`, `55` | When table props spawn and despawn. |
| `syncDistance` | `30` | Table updates are sent only to players within this distance. |
| `seatTimeoutSeconds` | `45` | A seat with no bet is released after this time. |
| `turnTimeoutSeconds` | `60` | An inactive player stands automatically. |
| `resultSeconds` | `12` | How long the result stays up. |
| `dealDelayMs`, `cardDelayMs` | `1800`, `650` | Pauses before the deal and between cards. |
| `actionCooldownMs`, `actionAnimationMs` | `350`, `1500` each | Lock between actions while the animation plays. |

### Camera, UI, blips, models

- `Config.Camera`: `enabled`, `defaultView` (`0` is the normal game camera), `transitionMs`, and a `views` list. Each view has `type` (`'table'` or `'chair'`), `offset`, `rotation`, and `fov`. The camera key cycles through the views.
- `Config.UI`: `playerNameMode` is `'nick'`, `'first'`, `'last'`, `'full'`, or `'number'`. The other keys (`dealer`, `players`, `ownHand`, `result`, `status`, `rules`) show or hide parts of the interface.
- `Config.Blips`: `enabled`, `sprite` (default `'blip_mg_blackjack'`), `scale`.
- `Config.Models`: `table`, `dealerChair`, `playerChair`, `cardBack`.

### Discord logging

In `configs/configServer.lua`, set `ConfigSrv.discordWebhook.enable = true` and paste your webhook into `url`. `logCompletedRounds` controls whether finished rounds are posted. The `language` table holds the text used in the log message.

## Commands

| Command | Who | What it does |
| --- | --- | --- |
| `blackjacklayoutdebug` | Client | Toggles the layout overlay that draws table centres, seats, chair headings, and interaction ranges. Same as `Config.Debug.Layout`. Use it while placing a table. |

## Money handling

- Bets are taken from the player's cash and winnings are paid to cash, through gumApi.
- If a player leaves or disconnects after betting, the stake stays in play. The hands stand automatically and are settled as normal.
- A payout that could not be delivered (for example the player was offline) is paid the next time that character logs in.
