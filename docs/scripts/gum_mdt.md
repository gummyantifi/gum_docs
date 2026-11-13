# GUM MDT

GUM MDT is a records system for law and government jobs on RedM: person records, cases, prisoners, documents, and wanted persons. It ships together with a bounty hunt system, and its updates are published under the name `gum_mdt_bountyhunt`.

## At a glance

- Video: [GUM MDT on YouTube](https://youtu.be/oEA3y0GuMc8)
- Store: [MDT + Bounty hunt on Tebex](https://gum-scripts.tebex.io/package/4804557)
- Frameworks: the release announcement lists VORP and RedEM. The changelog also contains fixes specific to RSG Core.

## What it does

### Access control

- Each MDT can be locked to specific jobs in your configuration.
- An MDT can be restricted by coordinates (where it can be opened) or by an item in the inventory.

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

### Wanted persons and prisoners

- A wanted persons registry linked to the bounty boards.
- A prisoners category. Prisoners can also be removed from the wanted registry.

### Search and audit trail

- Search across all notes. Searching "robbery" returns every case that mentions robbery.
- Each entry records who created it and who last edited it, with timestamps.

### Bounty hunting (v1.0.6)

- A bounty hunting system with configurable targets, spawn locations, delivery points, and cooldowns.
- Players capture targets and deliver them. The script checks deliveries to prevent invalid ones.
- The server tracks bounty availability and delivery progress, and can restore a carried target if the carrier disconnects.
- Bounty NPCs react to nearby players, line of sight, and gunshots. They investigate sounds, search for threats, and return to their patrol area.
- Map indicators for active bounties, with configurable visibility and distance.
- Optional debugging tools for administrators to inspect NPC perception and bounty behavior.

The developer recommends watching the video, because the script is large and the video explains each part.

## Before you install

Install **gumApi** first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).

## Configuration notes

### Bounty NPC options

v1.0.6 (2026-11-01) added configuration options for perception range, recognition time, night vision, hearing, patrols, and search behavior, along with visibility and distance options for the bounty map indicators. The changelog does not name the individual keys.

### Translation lines

Added 2026-07-03:

```lua
[21] = {"MDT", "Person does exist. First u need save them.", 2},
```

Added 2025-07-06:

```lua
    [17] = {"MDT", "No one player is not near you", 2},
     [112] = {'Give or remove documents?', 'Give', 'Remove'},
     [113] = {'Player ID', 'Accept'},
     buttonBountyBoardPrompt = "Bounty Board",
```

## Common problems and fixes

### Fingerprints are saved several times for the same person

Generating fingerprints saved duplicates, one for each time the MDT had been opened. This was fixed on 2026-07-03. Update to that version or later.

### Fingerprints cannot be created on RSG Core

This was fixed on 2026-08-13 in the client and server files. Update to that version or later.

### ID cards do not load

Loading of ID cards was fixed on 2026-08-13. Update to that version or later.

### On RSG, fines are not created when creating a new entry

This was fixed on 2026-08-20 in the client files. Update to that version or later.

### Anyone can remove a wanted entry from the Wanted Board

Removing a wanted entry did not check the player's job. This was fixed on 2026-08-20. Update to that version or later.

### A fine cannot be added to a new person

This is intended since 2026-07-03. Create and save the person first, then add the fine.

## Recent changes

- **2026-11-01, v1.0.6:** New bounty hunting system with configurable targets, spawn locations, delivery points, and cooldowns. Bounty NPCs now perceive players, line of sight, and gunshots, with new configuration options for perception and patrol behavior. Added map indicators for active bounties and optional debugging tools.
- **2026-08-20:** Removing a wanted entry from the Wanted Board now checks the player's job. Fixed fines not being created on RSG when creating a new entry. Client files changed.
- **2026-08-13:** Fixed creating fingerprints on RSG Core. Fixed loading of ID cards. Client and server files changed.
- **2026-07-03:** ID card photos fit the frame better. Fines can no longer be added to a new person before that person is created. Fixed duplicate fingerprints. The Documents Menu, Documents, and ID Cards use a new border style, and ID cards have better text positioning. Client, server, CSS, and JS files changed. Add the new translation line `[21]`.
- **2025-11-04:** Full overhaul of the MDT with a redesigned UI, categorized citizen records, fingerprints, the fines assistant, cases with evidence and locking, category permissions, document text color and fonts, the Document Archive, the wanted persons registry, and search across all notes. It was released as a test version. It is compatible with data from the earlier beta version, which converts automatically. Upload the entire folder when updating, because many configuration and language files changed.
- **2025-07-06:** Added missing translation lines (`[17]`, `[112]`, `[113]`, `buttonBountyBoardPrompt`) and some server edits. Client, clientBounty, and server files changed.

*This page was built from the script's announcement, its changelog, and resolved support tickets. For anything not covered here, open a ticket in the GUM Scripts Discord.*
