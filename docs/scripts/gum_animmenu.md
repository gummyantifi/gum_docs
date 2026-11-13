# GUM Animmenu

GUM Animmenu is an animation menu for RedM with over 250 animations, scenarios, shared interactions, props, walking styles, and a built-in sitting system. Players can save favorites, bind keys, and trigger animations from chat commands.

## At a glance

- Video: [GUM Animmenu](https://www.youtube.com/watch?v=11nwspu-oaI)

## What it does

### Animation library

- Over 250 animations are included.
- The library covers standard animations, scenarios, shared animations, prop animations, and walking styles.
- Animations are sorted into categories, and the search filter searches across all categories at once.
- An animation preview is available. It works only for the "animNorm" functions.

### Favorites and quick access

- Players can add animations to their personal favorites.
- Up to 32 favorite animations can appear in the center of the animation menu.
- Players can assign custom chat commands to animations (for example `/a sit`, `/a lean1`, `/a lean2`).
- The same system supports custom commands (for example `/reloadskin`).

### Adding your own content

Through the configuration files you can add:

- Custom animations and scenarios.
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
- Beds.
- Bathtubs.

The sitting system can be disabled.

### Extra activities

- Spin revolvers.
- Play with knives.
- Play dice with friends.
- The `gum_play` interactions are integrated into the animation menu.

## Before you install

Install **gumApi** first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).

## Configuration notes

### Cancel command

- `Config.CancelAnimationCommand`: the chat command that cancels the current animation. Setting it to nil disables it. Added 2024-12-18.

```lua
Config.CancelAnimationCommand = "clear"--Nil is disabled
```

### Key binds

- `Config.Keybind`: lets you bind your own animation keys. Before this, binds were limited to `1, 2, 3, 4, 5...`. Added 2026-07-13.
- `keybind.lua` (root folder): contains the function that handles custom key combinations. You can use it to create bindings such as `CTRL + 1`, other key combinations, or more binds than the default. Added 2026-07-13. Version 1.0.3 added a condition to this file that checks whether a specific control is pressed before a command runs.

### Sitting

- `configSitting`: a new configuration file added on 2026-07-08 with the integrated sitting menu.

### Favorites

- Commands with spaces are blocked when an animation is selected for favorites. Only text without spaces is allowed (since 2025-02-02).

### Dragging language entries

Added 2024-09-28:

```lua
    [17] = "testDrag", -- Command for dragging a player
    [18] = "[W][A][S][D]",
    [19] = "R. Hard [ESC]",
    [20] = "R. Easy [ENTER]",
    [21] = "Accept your hard drag?",
```

On 2024-09-29, entries `[19]` and `[20]` changed to a name and key format:

```lua
    [19] = {"R. Hard [ESC]", 0x156F7119},
    [20] = {"R. Easy [ENTER]", 0xC7B5340A},
```

### Playing an animation from another script

An event for calling animations from separate scripts was added on 2025-03-30:

```lua
-- props={"p_group_gamble_bottlewine01x", "skel_l_hand", 0.07, -0.04, 0.08, 252.0, 0.0, 0.0}
TriggerEvent("gum_animmenu:playAnim", anim, dics, time, flag, props)
```

## Common problems and fixes

### The hostage animation stops unexpectedly

The right mouse button cancels the hostage animation (added 2025-07-06). Pressing it while holding a hostage ends the animation.

### A favorite command with a space in it is not accepted

This is intended. Since 2025-02-02, commands chosen for favorites cannot contain spaces. Use a single word.

### A prop stays in the player's hand after the animation is canceled

Fixed on 2024-12-18 (client folder).

### The script uses a lot of client resources

Fixed on 2024-10-25. Replace all files with the updated version. The config file does not need to be updated for that release.

### I cannot find the updated files

Updated files are delivered to the purchaser's keymaster account. Download the new version from there.

## Recent changes

- 2026-10-31: Version 1.0.3. Font family changed from "crock" to "koala" in `html/index.css`. Added a control-pressed check in `keyBind.lua`. Replace `html/index.css`, `keyBind.lua`, and `client/client.lua`.
- 2026-07-13: Added custom animation key binds through `Config.Keybind` and a new `keybind.lua` file in the root folder (client and fxmanifest changed).
- 2026-07-08: Full script update. Redesigned backgrounds for the Animations, Props, and Favorite Animations menus. Search now covers all animation categories. The Sitting Menu and `gum_play` are integrated into the animation menu. Improved dice game. Existing animations and content are unchanged. New `configSitting` file, new images, and some new `config.lua` data and language entries.
- 2025-07-06: The hostage animation can be canceled with the right mouse button. Added an animation preview (only for the "animNorm" functions).
- 2025-03-30: Added the `gum_animmenu:playAnim` event for calling animations from other scripts.
- 2025-02-02: Commands with spaces are blocked when selected for favorites.
- 2024-12-18: Added `Config.CancelAnimationCommand`. Fixed the prop not being removed from the hand when an animation is canceled.
- 2024-12-16: Major update. Added hostage taking, crawling, and the sack over the head. Over 150 new animations, new categories, a rewritten translation, and reorganized folders. Replacing all files is recommended.
- 2024-10-25: Faster animation loading, lower client resource use, better drag sync between clients, and a redesigned menu with the option to bind multiple animations. Replace all files except the config.
- 2024-09-29: Drag language entries `[19]` and `[20]` changed to a name and key format. Skeleton attachment for dragging changed.
- 2024-09-28: Added a more forceful drag along the ground, with new language entries `[17]` to `[21]`.

*This page was built from the script's announcement, its changelog, and resolved support tickets. For anything not covered here, open a ticket in the GUM Scripts Discord.*
