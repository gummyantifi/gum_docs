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
- Drag players.
- Lockpick players.
- Put players into a prisoner outfit.
- Load up to 3 players into wagons.
- Hang players.

### Jailing

- Jailing and unjailing are available to specific jobs.
- The jail script is limited on range.
- Custom jobs are supported. If you have looping animations, you can add more.

### Escapes and robbery

- Prisoners can escape through NPC quests, which are customizable.
- Robbery is included.

### Storage

- Everything works on SQL.

## Before you install

Install **gumApi** first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).

## Common problems and fixes

### Cheaters can send everyone to jail

Fix: update `server.lua` to the current version. This was the developer's answer when the problem was reported in July 2026.

## Recent changes

- 2024-11-15: Added protection for `gum_mdt`, because the script has to check whether MDT exists or not. Replace `server.lua`.

*This page was built from the script's announcement, its changelog, and resolved support tickets. For anything not covered here, open a ticket in the GUM Scripts Discord.*
