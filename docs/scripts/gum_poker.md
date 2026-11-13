---
icon: coins
---

# GUM Poker

GUM Poker is a multiplayer poker system for RedM with two variants, Texas Hold'em and Five Card Draw. It uses physical cards and chips on configurable tables.

## At a glance

- Video: [GUM Poker](https://youtu.be/yPmbh1uyr7o)
- Store: [GUM Poker on Tebex](https://gum-scripts.tebex.io/package/7786110)
- Bundle with GUM Blackjack: [Poker + Blackjack](https://gum-scripts.tebex.io/package/7786138)

## What it does

### Two poker games

- Texas Hold'em and Five Card Draw are both included. Each table runs one of them.
- Each game has complete betting rounds and hand evaluation.
- The winner is determined and paid out automatically.
- Dealer button rotation and blinds are included.
- Five Card Draw includes card exchanging and an ante.

### Multiplayer tables

- 2 to 6 seats per table.
- You can run multiple independent poker tables.
- Cards, chips, bets, and results are synchronized between players.
- Play is turn based and rounds progress automatically.

### Betting

- Players can Check, Call, Bet, Raise, Fold, and go All-In.
- Minimum and maximum buy-ins are configurable per table.
- Blinds, the raise step, and the Five Card Draw ante are configurable per table.
- The pot and payouts are managed automatically, in dollars from the player's cash.

### Presentation

- Physical cards and poker chips on the tables.
- Dealing, betting, and folding animations.
- Multiple customizable camera views.
- An interactive poker interface with a rules and guide page.
- A choice of table, chair, and card deck models per table.
- Custom player seating arrangements.

### NPC poker trainer

- Players can practice poker against NPC opponents.
- NPC models and names are configurable.
- The number of NPCs and their decision delay are adjustable.
- Practice mode does not use player money.

### Other options

- Custom poker table locations.
- Individual settings for each table.
- Configurable interaction keys.
- Adjustable game timers and action delays.
- Optional map blips and Discord logging.

## Before you install

Install gumApi first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md). `fxmanifest.lua` lists `gumApi` as the only dependency.

## Install

1. Put the `gum_poker` folder in your resources.
2. Start it after gumApi:

```cfg
ensure gumApi
ensure gum_poker
```

There is no SQL file to import and no item to add. The script does not use a database. Buy-ins and unpaid winnings are kept in the resource's own server KVP storage, so they survive a restart.

## Configuration

All editable files are in `configs/`.

| File | What it holds |
| --- | --- |
| `configs/configMain.lua` | Keys, UI, trainer, game timers, distances, debug |
| `configs/configTables.lua` | Model lists (`Config.PokerStyles`) and the table list (`Config.Tables`) |
| `configs/configCamera.lua` | Camera views (`Config.Camera`) |
| `configs/configPresentation.lua` | Card and chip placement on the table (`Config.Presentation`) |
| `configs/configLanguage.lua` | All player-facing text, in `Config.Text` |
| `configs/configServer.lua` | Discord webhook, in `ConfigSrv.discordWebhook` (server only) |

### Tables: `Config.Tables`

Each entry is one table, keyed by a text id (for example `Valentine`). The default config ships five Hold'em tables: Valentine, Blackwater, SaintDenis, Armadillo, and Rhodes.

| Key | Meaning |
| --- | --- |
| `enabled` | Set to `false` to turn the table off. |
| `variant` | `'holdem'` or `'fiveCardDraw'`. |
| `maxDraw` | Five Card Draw only. Most cards a player can exchange (0 to 5, default 3). |
| `ante` | Five Card Draw only. Paid by each player before the deal. Must be above 0 on a Five Card Draw table. |
| `label`, `description` | Text shown for the table. |
| `guide` | `rules` and `help` show or hide the Rules and Guide pages for this table. |
| `blip` | `enabled`, `name`, `sprite` (default `'blip_mg_poker'`), `scale`. |
| `coords` | `{x, y, floor z, heading}` of the table centre. |
| `tableStyle`, `chairStyle`, `cardStyle` | Index into the lists in `Config.PokerStyles` (8 tables, 5 chairs, 5 card sets). |
| `spawnTable`, `spawnChairs` | Set to `false` to use a table or chairs that already exist on the map. |
| `maxPlayers` | Number of seats, 2 to 6. |
| `smallBlind`, `bigBlind` | Blinds in dollars. Defaults are 0.04 and 0.09. |
| `raiseStep` | Amount one key press changes the bet, in dollars. |
| `buyin` | `minimum`, `maximum`, `default`, `step`, in dollars. Defaults are 5, 20, 10, and 1. |
| `surfaceHeight` | Height of the card and chip surface above `coords` z. Tune it to the tabletop. |
| `chairZ`, `sitZ`, `radiusX`, `radiusY`, `angle` | Chair height, seated height, and the size and rotation of the seat ring. |

The script checks the table values at start. A table with invalid stakes stops the resource with `Invalid poker table stakes: <id>`. The rules it checks: the big blind is at least the small blind, the minimum buy-in is at least the big blind, the default buy-in is between minimum and maximum, and a Five Card Draw table has an ante above 0.

### Keys: `Config.Prompts`

- `newPrompt` (default `true`) chooses between the two gumApi prompt systems.
- `keys` maps each action to a key. Defaults: `open` E, `buyin`, `ready`, and `confirm` left mouse, `call` E, `fold` F, `raise` R, `less` and `more` mouse wheel, `camera` C, `test` X, `rules` Y, `leave` Backspace.

`Config.KeyMapping` (`enabled = true`, `prefix = 'GUM Poker'`) registers the prompt keys in the RedM key binding settings under that prefix.

### Trainer: `Config.Trainer`

The trainer is the practice game against NPCs, started with the `test` key (X by default) while seated.

| Key | Default | Meaning |
| --- | --- | --- |
| `botCount` | `5` | Most NPCs added. They fill empty seats only. |
| `stack` | `50` | Practice dollars for each seat. No money leaves the player's cash. |
| `thinkMs` | `1200` | Delay before an NPC acts. |
| `models`, `fallbackModel` | town folk models | Ped models for the NPCs. |
| `names` | five names | NPC display names. |

The trainer is available only while `Config.Debug.Trainer` is `true` (the default). Set it to `false` to remove practice mode.

### Game timing and distances

`Config.Game`:

| Key | Default | Meaning |
| --- | --- | --- |
| `turnSeconds` | `35` | Time a player has to act. Minimum 5. |
| `resultSeconds` | `10` | How long the result stays up. |
| `streetDelayMs` | `1500` | Pause between betting rounds. |
| `idleSeconds` | `90` | A seat with no chips is closed after this time. |

`Config.World`: `interactionDistance` (`1.5`), `serverDistance` (`3.5`), `spawnDistance` (`45`), `despawnDistance` (`65`), `syncDistance` (`30`), plus request and model timeouts.

### UI, camera, presentation

- `Config.UI`: `playerNameMode` is `'number'`, `'nick'`, `'first'`, `'last'`, or `'full'`. `elements` shows or hides `playerList`, `board`, `hand`, `amount`, and `status`.
- `Config.Camera`: `enabled`, `defaultView` (`0` is the normal game camera), `transitionMs`, and a `views` list. Each view has `type` (`'table'` or `'chair'`), `offset`, `rotation`, `fov`, and an optional `variant` to limit it to one game.
- `Config.Presentation`: where cards, the deck, and chips sit on the table, plus chip models. `referenceDeck.enabled` shows a deck on the table that helps you line up `surfaceHeight`. Chips are visual only. The interface shows the exact money.

### Discord logging

In `configs/configServer.lua`, set `ConfigSrv.discordWebhook.enable = true` and paste your webhook into `url`. `logCompletedHands` controls whether finished hands are posted. The `language` table holds the text used in the log message.

## Commands

| Command | Who | What it does |
| --- | --- | --- |
| `pokerlayoutdebug` | Client | Toggles the layout overlay that draws table centres, chair positions, and interaction ranges. Same as `Config.Debug.Layout`. Use it while placing a table. |

## Money handling

- Buy-ins are taken from the player's cash and chips are paid back to cash, through gumApi.
- A payout that could not be delivered (for example the player was offline) is paid the next time that character logs in.
