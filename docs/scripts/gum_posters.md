---
icon: scroll
---

# GUM Posters

GUM Posters gives your RedM server notice boards where players pin images by link. Boards can be tied to a city, shared globally, or reserved for events.

## At a glance

- Video: [GUM Posters on YouTube](https://www.youtube.com/watch?v=Q-5ZtjN7vec)
- Frameworks: RedEM, RedEM Reboot, VORP, GUM, RSG, QBR

## What it does

### Board types

- **City board:** shows only the posters pinned to that board.
- **Global board:** shows the posters of all boards except event boards.
- **Event board:** shows only the posters of event boards.

### Per-board settings

- A custom name (prompt text) and map marker for the board, with its own blip sprite, or no blip at all.
- Open the board by looking at it (targeting) or by standing at its coordinates.
- Use a board model of your choice, or turn the model off.
- Restrict the board by job: which jobs can pin posters, which can remove pinned posters, and which can see the names of the people who placed them.
- Block identical images from being pinned to the same board.
- A cleaner that deletes posters older than a set number of days.
- A separate Discord webhook per board.

### Base settings

- Show or hide the background, so you can style the board to your own taste. The appearance is edited with CSS.
- Turn the history of pinned images on or off. With history on, an image you placed on one board is offered again when you open another board. Once the image is removed from all boards, the history is empty.
- Turn camera targeting on or off.

### Images

- Images of any resolution and shape can be used. The script detects the image size and fits it onto the board, and clicking a poster shows it in its original proportions.
- When a player adds an image, the link is checked to confirm the image loads.

## Requirements

- **gumApi**. Install it first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).
- **oxmysql**.

## Install

1. Put the `gum_posters` folder in your resources and start it after `gumApi` and `oxmysql`.
2. You do not need to import any SQL and the script uses no items. On start it creates the `posters` table if it does not exist, with the columns `id`, `poster_link`, `city` (the board key), `date`, and `icName` (the name of the character who pinned the poster).
3. Updates are delivered to the purchaser's Keymaster account. Download new files from there.

## Configuration

The config is split into three files in the `cfg` folder.

### `cfg/config.lua`: base settings

| Key | Default | What it does |
| --- | --- | --- |
| `Config.UnemployedJob` | `"Unemployed"` | The job key used for players who match no other job in a board's `jobAccess`. |
| `Config.backgroundEnabled` | `true` | Shows the background image on the board and the history panel. |
| `Config.enabledHistory` | `true` | Shows the player's history of pinned images. |
| `Config.cameraTarget` | `false` | Points the camera at the board when it opens. It works only on boards that have a model. |
| `Config.Debug` | `true` | Prints debug lines. Set it to `false` on a live server. |

`Config.Language` holds the notification title and icon, the open prompt (`keyForOpen`, `openBoard`), and the pin and remove questions. `Config.DiscordHookLanguage` holds the labels used in the webhook message.

### `cfg/configCoords.lua`: the boards

`Config.Posters` has one entry per board. The key is the board's name. It is also saved with every poster in the `city` column, so do not rename a board that already has posters.

```lua
["Poster Board - Valentine"] = {
    type = "city",
    titleForPrompt = "Poster Board",
    blipSetting = {
        blipSprite = 1735233562,
        blipName = "Poster table",
    },
    baseSetting = {
        interaction = "target",
        coords = {-270.05, 807.20, 119.42, 100.0},
        model = "mp005_p_mp_bountyboard01x",
        jobAccess = { ... },
        blockSameImg = true,
        clearDays = 7,
    },
},
```

- `type`: `"city"`, `"global"`, or `"event"`. See Board types above.
- `titleForPrompt`: the text of the prompt at the board.
- `blipSetting.blipSprite`: the blip sprite. `0` disables the blip. `blipSetting.blipName` is its name on the map.
- `baseSetting.interaction`: `"target"` opens the board when the player looks at the board model. `"coords"` opens it when the player stands at `coords`.
- `baseSetting.coords`: x, y, z, heading. The model is placed here.
- `baseSetting.model`: the board model. Set it to `nil` for no model. With no model, use `interaction = "coords"`, because targeting needs the model.
- `baseSetting.blockSameImg`: `true` refuses a link that is already pinned on that board.
- `baseSetting.clearDays`: posters older than this many days are deleted.

#### Job permissions (`jobAccess`)

`jobAccess` is a list of job names. Each one sets three permissions:

```lua
jobAccess = {
    ["Sheriff"] = { canUse=true, canRemove=true, canSeeName=true },
    [Config.UnemployedJob] = { canUse=true, canRemove=false, canSeeName=false },
},
```

- `canUse`: can pin posters.
- `canRemove`: can remove posters.
- `canSeeName`: can see who pinned each poster.

Every board must keep the `[Config.UnemployedJob]` entry. It is the fallback for every player whose job is not listed, and the board does not open for them without it.

#### How the cleaner runs

The cleaner runs once, when the resource starts. It deletes old posters from all boards in one pass and is not limited to one board, so the shortest `clearDays` among boards that have posters applies to every board. Set the same `clearDays` on all boards to get a predictable result.

### `cfg/configSrv.lua`: Discord webhooks

`ConfigSrv.Posters` has one entry per board, with the same keys as `Config.Posters`.

```lua
["Poster Board - Saint Denis"] = {
    discordWebhook = {
        link = nil,
        color = 16711680,
        title = "Poster",
        icon = "",
        avatar = "",
        icName = true,
    }
},
```

- `link`: the webhook URL. `nil` sends nothing for this board.
- `color`, `icon`, `avatar`: the look of the message.
- `icName`: `true` adds the character name of the player who pinned the poster.

When you add a board to `cfg/configCoords.lua`, add an entry with the same key here too, even with `link = nil`. Pinning a poster fails on a board that has no entry in this file.

## Customers also ran into these

### Adding an image shows a yes/no choice with no field for the link

This was a bug in the client and JS files. It was fixed on 2025-03-12. Update to that version or later.

### The history background still shows with the background disabled

This was fixed on 2025-03-12. With `Config.backgroundEnabled = false` the current version hides both backgrounds. Update to that version or later.

### A poster with a very long image link does not save

The link was too long for the database column. This was fixed in `server.lua` on 2025-04-13, and the script now creates `poster_link` as `LONGTEXT`. The script does not change a `posters` table that already exists, so if yours was created by an older version, check that column's type.

### The camera does not target the center of the board

The camera target on the prop center was fixed in the client on 2025-03-13. Update to that version or later. `Config.cameraTarget` only works on boards that have a model.
