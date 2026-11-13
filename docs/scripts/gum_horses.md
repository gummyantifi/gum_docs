---
icon: horse
---

# GUM Horses

GUM Horses is a horse ownership system for RedM. It covers genetics and breeding, training, horse needs, customization, auctions, and trading between players.

## At a glance

- Video: [GUM Horses on YouTube](https://www.youtube.com/watch?v=pEDthWjLpwg)
- Store: [GUM Horses on Tebex](https://gum-scripts.tebex.io/package/7806421)

## What it does

### Genetics and breeding

- Foals inherit genetics from both parents, including coat colors, patterns, and appearance.
- Statistics and personality traits are inherited too.
- Breeding requirements and timers are configurable.
- Each horse has a pedigree, so bloodlines progress over generations.

### Training and progression

- Horses earn XP and build a bond with their owner.
- Trainable stats: speed, acceleration, agility, courage, health, and stamina.
- There are multiple training activities, and the experience rewards are configurable.
- Progression is tracked per horse.

### Horse care

- Horses have hunger, thirst, energy, happiness, and health.
- Horses can get sick and recover.
- Metabolism is dynamic and the environment affects the horse.
- Horses age and pass through life stages.
- Feeding and care are configurable.

### Customization

- A Horse Creator for building a horse's look, with custom coat colors, patterns, and textures.
- Saddles, bridles, blankets, lanterns, and other accessories, with color options per component.
- Horseshoes, each with its own durability.

### Ownership, trading, and stables

- Wild horse taming.
- Horse leading and rope mechanics.
- Horse cargo and storage.
- Horse branding.
- Horse showcase and auctions.
- A player-to-player horse marketplace, plus gifting and transfers.
- Job and faction stables.
- Configurable stable locations and services.

### Other features

- Horse calling, with the horse's world position saved.
- Classic and Modern UI themes.
- Configurable interaction prompts.

### Works with GUM Wagons

GUM Horses and GUM Wagons are built to work together. Players can use the horses they own to pull wagons, and the horses' combined statistics influence wagon performance.

## Before you install

Install **gumApi** first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).

- Run the most recent version of gumApi.
- GUM Horses is a new script built from the ground up. It is a separate product from `gum_stables`, which is still maintained.

## Configuration notes

### Restrict horse purchases to specific accounts

Added in v1.0.18 (2026-11-12), in `configs/cfgHorses.lua`:

```lua
identifierForBuy = {},
```

The changelog gives this commented example and explanation:

```lua
-- identifierForBuy = { 'steam:110000112345678' }, -- Optional account purchase whitelist.
    -- Missing/nil/{} allows everyone. Raw hex is accepted; existing job/stable rules still apply.
```

So an empty or missing `identifierForBuy` lets everyone buy. When you list identifiers, only those accounts can buy, and your existing job and stable rules still apply.

### Body proportions

v1.0.21 (2026-11-13) brought back the body modification feature from `gum_stables`. The patch notes list `configs/horseBody.lua` as the config to load for it.

The same version added camera controls to the `/horsecreator` command.

### Translation lines

v1.0.21 (2026-11-13) added these lines to `configLanguage.ui`:

```lua
        horseCreatorCamera = 'Camera',
        horseBodyTitle = 'Body proportions',
        horseBodyHint = "Adjust individual features. Zero keeps the model's original shape.",
        horseBodySearch = 'Find a feature',
        horseBodyReset = 'Reset body',
```

## Common problems and fixes

### The auction NPC is in the wrong position when the auction is disabled

This was a bug in the script. It was fixed in v1.0.18. Update to v1.0.18 or later.

## Recent changes

- **2026-11-13, v1.0.21:** Body modification from `gum_stables` is back. `/horsecreator` now has camera management. Horse loading is slightly improved. To update: replace the `client`, `shared`, `server`, and `html` folders, replace `fxmanifest.lua`, load the config `configs/horseBody.lua`, and add the new `configLanguage.ui` lines shown above.
- **2026-11-13, v1.0.20:** The shop now initializes when the character is loaded, not before. The change is in `client/store.lua`.
- **2026-11-12, v1.0.18:** Added support for buying by Steam identifier (`identifierForBuy`). Fixed the auction NPC position when the auction is disabled. To update: add `identifierForBuy = {},` to `configs/cfgHorses.lua` and replace `client/auction.lua`, `client/myHorse.lua`, `client/store.lua`, `server/auction.lua`, `server/store.lua`, and `shared/functions.lua`.

*This page was built from the script's announcement, its changelog, and resolved support tickets. For anything not covered here, open a ticket in the GUM Scripts Discord.*
