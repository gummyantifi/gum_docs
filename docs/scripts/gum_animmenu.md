---
icon: person-running
---

# GUM Animmenu

GUM Animmenu is an animation menu for RedM with over 250 animations, scenarios, shared interactions, props, walking styles, and a built-in sitting system. Players can save favorites, bind keys, and trigger animations from chat commands.

## At a glance

- Video: [GUM Animmenu](https://www.youtube.com/watch?v=11nwspu-oaI)

## What it does

### Animation library

- Over 250 animations are included.
- The library covers standard animations, emotes, scenarios, shared animations, prop animations, and walking styles.
- Animations are sorted into categories, and the search filter searches across all categories at once.
- Hovering an animation in the list plays a preview on a small preview character. The preview plays for `animNorm` and `emote` entries.

### Favorites and quick access

- Players can add animations to their personal favorites.
- Up to 32 favorite animations can appear in the center of the animation menu.
- Players can assign their own chat command to an animation (for example `/a sit`, `/a lean1`, `/a lean2`).
- The same system runs other chat commands from the menu (for example a skin reload command).

### Adding your own content

Through the configuration files you can add:

- Custom animations, emotes, and scenarios.
- Shared animations.
- Prop animations and props attached to the player.
- Walking styles.
- Custom commands.

### Player interactions

- Take hostages or become a hostage.
- Drag another player along the ground.
- Crawl on the ground.
- Hug other players.
- Lie down together.
- Place a sack over another player's head, which blocks their vision.

### Sitting system

Players look at a compatible object to get interaction options. Supported objects:

- Chairs.
- Benches (left and right side).
- Beds and bedrolls.
- Bathtubs.
- Pianos.

The sitting system can be disabled.

### Extra activities

- Spin revolvers.
- Play with knives at a table.
- Play dice with friends.
- The `gum_play` interactions are integrated into the animation menu.

## Requirements

- **gumApi**. Install it first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).
- **oxmysql**. The script saves player commands and favorites through it.

## Install

1. Put the `gum_animmenu` folder in your resources and start it after `gumApi` and `oxmysql`.
2. You do not need to import any SQL. On start the script creates two tables if they do not exist: `animations` (each character's own animation commands) and `animationsfavorite` (each character's favorite slots).
3. Add an item named `HeadBag` to your inventory items. Using it puts the sack on your own head or on the nearest player. The item name is fixed in the script.

## Configuration

There are three files in the root folder: `config.lua`, `configSitting.lua`, and `keyBind.lua`.

### Opening the menu (`config.lua`)

| Key | Default | What it does |
| --- | --- | --- |
| `Config.AnimMenuKeyPress` | `true` | Open the menu with a key. |
| `Config.AnimMenuKey` | `0x0F39B3D4` | The control hash for that key. |
| `Config.NotHoldKeye` | `false` | `true` opens the menu on a single press. `false` requires holding the key. |
| `Config.OpenMenuCommandEnable` | `false` | Open the menu with a chat command. |
| `Config.OpenMenuCommand` | `"animmenu"` | The name of that command. |
| `Config.Debug` | `true` | Prints debug lines. Set it to `false` on a live server. |

### Commands (`config.lua`)

| Key | Default | What it does |
| --- | --- | --- |
| `Config.AnimCallCommand` | `"a"` | The command players use to call their saved animations, for example `/a sit`. |
| `Config.CancelAnimationCommand` | `"clear"` | Cancels the current animation. Set it to `nil` to disable the command. |
| `Config.Commands.drawPerson` | `"drawPerson"` | Drag the nearest player along the ground. |
| `Config.Commands.layOnGround` | `"layOnGround"` | Crawl on the ground. |
| `Config.Commands.selfHostige` | `"selfHostige"` | Offer yourself as a hostage. |
| `Config.Commands.personHostige` | `"personHostige"` | Take the nearest player hostage. You must hold a melee weapon. |
| `Config.Language.gunPlayCMD` | `"gun"` | Starts the revolver spinning. |
| `Config.Language.diceCMD` | `"dice"` | Rolls dice. |

The key names `selfHostige` and `personHostige` are spelled this way in the script. Keep the spelling.

### Key binds (`config.lua` and `keyBind.lua`)

- `Config.AllowKeybind` (default `true`) turns on quick keys for favorites.
- `Config.Keybind` maps a favorite slot number to a control hash. The default binds slots 1 to 8.

```lua
Config.Keybind = {
	[1] = 0x52D29063,
	[2] = 0x1CE6D9EB,
	-- ...
}
```

- `keyBind.lua` (root folder, capital B) holds the `keyPressAnim()` function that reads `Config.Keybind`. Edit it to require a key combination such as `CTRL + 1`, or to add more binds. It already checks a second control before it runs a favorite.

### Mini games and sitting (`config.lua`)

| Key | Default | What it does |
| --- | --- | --- |
| `Config.DisablePlayGames` | `false` | `true` disables the knife game, the weapon spinning, and the dice game. |
| `Config.FlourishChance` | `10` | Chance of the rare knife game animation. |
| `Config.knifePlayTables` | 3 table models | The table models where the knife game is offered. |
| `Config.DisableSitting` | `false` | `true` disables the sitting menu. |

### Props (`Config.Prop` in `config.lua`)

Each entry is a prop that players can attach to their character.

```lua
[7] = {name="Bagpack 1", prop="p_ambpack01x", bone=41232, parameter={0.33, -0.12, -0.19, 0.00, -56.0, 0.00}, jobLock={}},
```

- `name`: the label in the menu.
- `prop`: the model name.
- `bone`: the bone the prop attaches to.
- `parameter`: position x, y, z and rotation x, y, z.
- `jobLock`: job names that can use the prop. Leave it empty for everyone.

### Animations (`Config.Anim` in `config.lua`)

Each entry has a `name`, an optional `category`, and an `itsAnimation` type. The type decides which other fields the entry needs.

| `itsAnimation` | Extra fields |
| --- | --- |
| `"animNorm"` | `anim = {dict, name}`, `time`, `flag` |
| `"animWithProp"` | `anim`, `time`, `flag`, `props = {model, bone, x, y, z, rx, ry, rz}` |
| `"emote"` | `emote` (the emote name) |
| `"scenario"` | `scenario`, `time` |
| `"animShared"` | `animDict`, `animName`, `animDict2`, `animName2`, `delay1`, `delay2`, `time`, `flag`, `offset` |
| `"walkingStyle"` | `walkAnim` |
| `"command"` | `command` (a chat command to run, without the slash) |

Entries without a `category` appear under the label in `Config.Language.uncategorized`. The default config ships a `Commands` category with entries such as `undress`, `dress`, `hat`, and `house_manage`. Those commands belong to other scripts, so remove the entries your server does not have.

### Sitting (`configSitting.lua`)

- `Config.InteractAnim` lists the poses offered for each object type: `Bath`, `Bed`, `Bedroll`, and `Sit`. A pose can be limited with `isCompatible = "male"` or `"female"`.
- `Config.Props` lists the object models that can be used, in the groups `Pianos`, `Baths`, `OneBed`, `DoubleBeds`, `OneSit`, and `DoubleSit`. Each model has an offset (`x`, `y`, `z`), a heading (`h`), and a `type` that points to a group in `Config.InteractAnim`. Double models have two positions, one for each side.

To support a new chair or bed model, add its model name to the matching group with an offset.

### Language (`Config.Language` in `config.lua`)

All menu text, prompts, and notifications are in `Config.Language`. The drag prompts are in `Config.Language.drawText`:

```lua
drawText = {"[W][A][S][D]", "R. Hard [BACKSPACE]", "Cancel [ENTER]"},
```

## Playing an animation from another script

Trigger this client event from your own script:

```lua
-- props is optional, for example:
-- props = {"p_group_gamble_bottlewine01x", "skel_l_hand", 0.07, -0.04, 0.08, 252.0, 0.0, 0.0}
TriggerEvent("gum_animmenu:playAnim", anim, dics, time, flag, props)
```

To cancel the current animation from another script, trigger the client event `gum_animmenu:cancel`.

## Customers also ran into these

### The hostage animation stops unexpectedly

The aim control (right mouse button by default) cancels the hostage animation. Pressing it while holding a hostage ends the animation.

### A favorite command with a space in it is not accepted

This is intended. A command with a space is ignored when you save it. Use a single word. The command is also saved in lower case.

### A prop stays in the player's hand after the animation is canceled

This was fixed in the client files on 2024-12-18. Update to that version or later.

### The script uses a lot of client resources

This was fixed on 2024-10-25. Replace all files with the current version.

### I cannot find the updated files

Updated files are delivered to the purchaser's keymaster account. Download the new version from there.
