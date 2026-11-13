---
icon: gem
---

# GUM Treasure

GUM Treasure is a treasure hunting script for RedM. Players follow maps to a spot, dig with a shovel, and collect rewards.

## At a glance

- Video: [Treasure hunting](https://youtu.be/isYgWI2NQ7w)
- Supported frameworks: RedEM, RedEM Reboot, VORP, GUM, QBR, RSG

## What it does

### Maps and items

- Add custom images for maps and items.
- Choose which maps a specific store offers.
- Generate preset maps at random, with options to generate all of them or reset them.
- Follow the included guide for creating new maps. It explains how to make custom maps in Photoshop that match the visual style you want.

### Searching and digging

- Once a player has searched a treasure, another player cannot search the same treasure again.
- Digging uses shovel animations for holding and digging.
- Searching has a configurable chance, and digging on the same coordinates is protected.

### Rewards and logging

- Rewards can be items, money, or gold, each with configurable chances.
- Safe treasures are supported and are compatible with Qadr-Safe.
- Discord webhooks are supported.

## Before you install

Install **gumApi** first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).

Qadr-Safe is an optional integration, used for safe treasures.

## Recent changes

- 2026-05-31: Major update that is close to a full redesign. The configuration file was completely overhauled and is much more extensive, and there are new reward options.
- 2025-06-16 (beta): Fixed the camera being different on characters with different scales.
- 2025-06-04 (beta): Beta version released with store-specific maps, random generation of preset maps, Discord webhook support, an improved digging animation, expanded rewards (items, money, gold) with configurable chances, and a guide for creating new maps.
- 2024-11-15: Maps now reset after the server restarts. Before this, the reset had to be done manually. Replace the client and server files.

*This page was built from the script's announcement, its changelog, and resolved support tickets. For anything not covered here, open a ticket in the GUM Scripts Discord.*
