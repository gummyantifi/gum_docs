# gumApi: troubleshooting

This page lists the gumApi problems that support tickets and the changelog have already solved, plus what changed in recent updates.

## Check these first

1. gumApi is on the server at all. It is a separate download and is not inside any script package. This was the most common ticket by a wide margin.
2. Start order: framework first, then gumApi, then the GUM scripts.
3. The name in `server.cfg` is exactly `gumApi`, the same as the resource folder.
4. On a gumApi older than 2026-10-20, `Config.Framework` is set to your framework. From 2026-10-20 on, gumApi no longer needs a framework selected.
5. gumApi is current, and the download is complete. New script updates expect the new gumApi.

## Problems and fixes

### Getting gumApi

#### I bought a script and it says it needs gumApi, but gumApi was not in my files

Cause: gumApi is not delivered by email or through your CFX assets. It is downloaded from the GUM Scripts Discord, in a channel you only see once you have the customer role.

Fix: open a role request ticket in the Discord and give the Tebex transaction ID from your purchase email. If you no longer have the email, look the ID up at https://checkout.tebex.io/payment-history/login. Once the role is assigned, the download channel becomes visible.

#### I cannot see the gumApi download channel anymore

Cause: the role is gone. Customers who left the Discord and joined again lost their role and, with it, the channel.

Fix: open a role request ticket again with your Tebex transaction ID.

#### I am a developer and the owner has the scripts, not me

The server owner opens a ticket and asks for the developer role for you. Support needs your actual Discord username, not your display name.

### Errors at startup

#### "attempt to call a nil value (field 'registerItem')" in a GUM script

The same cause produces `(field 'getInformation')` and `(field 'checkPay')`.

Cause: the script cannot reach gumApi. In the tickets this was one of three things:

- gumApi was not installed (the most frequent).
- gumApi started after the script.
- On an older gumApi, the framework in the gumApi config was not changed to the server's framework.

Fix: install gumApi, start it after the framework and before the script, and on older versions set the framework.

#### "Couldn't find resource gumAPI."

Cause: the name in `server.cfg` is spelled differently from the resource folder. `gumAPI` and `gumApi` are different names.

Fix: use `gumApi` in `server.cfg`, exactly as the folder is named, then restart.

#### "No such export vorp_inventoryApi in resource vorp_inventory"

```text
SCRIPT ERROR: gumApi/serverData/serverCompVorp.lua:14: No such export vorp_inventoryApi in resource vorp_inventory
```

Cause: gumApi started before the framework.

Fix: run gumApi after the framework, and run the scripts after gumApi.

#### "No such export gum_inventoryApi in resource gum_inventory"

```text
SCRIPT ERROR: gumApi/serverData/serverCompGum.lua:13: No such export gum_inventoryApi in resource gum_inventory
```

Cause: the framework in the gumApi config is not set to yours.

Fix: on a gumApi older than 2026-10-20, edit the framework in the gumApi config to your own. On 2026-10-20 or newer there is nothing to select.

#### "attempt to index a nil value (field 'Language')" or warnings about `configLanguage.lua`

Cause: the first package of the 2026-10-20 update was missing `configLanguage.lua`. The file is listed in `fxmanifest.lua`, so its absence gave two startup warnings and this error in `serverData/serverCompVorp.lua`.

Fix: download gumApi again. The package was corrected on 2026-10-20 and 2026-10-21 and now includes `configLanguage.lua`.

#### oxmysql warns about an oversized result set from gumApi

```text
Warning: gumApi executed a query with an oversized result set (1302 results)!
SELECT * FROM items
```

The developer confirmed this is normal. Nothing to fix.

#### "attempt to call a nil value (field 'getAllItems')"

`getAllItems` is a gumApi function. In the ticket where a script threw this, the developer's answer was that the installed gumApi was old. Update gumApi.

### After updating

#### Inputs or prompts in a script behave differently after a gumApi update

Cause: the 2026-05-31 gumApi update changed how inputs, answer inputs, and multiple inputs work (for example, ESC now closes them and always selects the second option). Most scripts received a matching update at the same time.

Fix: check portal.cfx for an update to each of your scripts and install it. Most of those changes are on the client side, but the developer recommends updating the server side too. A housing ticket about a prompt opening behind the menu was closed this way. The 2026-06-13 gumApi update also made inputs always display above all windows.

#### Do I need the new gumApi for a script update?

Yes for the newest ones. The 2026-10-20 changelog entry states that the new gumApi will be required by the upcoming Ecology, Farming, and Housing updates and by new scripts. Those scripts will still offer the native prompt system as an option.

### Frameworks

#### We are switching framework. Do we need different script versions?

No. The scripts stay the same. On 2026-10-18 the developer's answer for a move from VORP to RSG was to change only the framework in gumApi. Since 2026-10-20, gumApi no longer needs it selected.

#### RSG 2.0

On versions with a framework setting, change `"RSG"` to `"RSG2"` in the config (changelog 2024-11-18). The developer stated on 2026-08-03 that gumApi is mainly compatible with VORP and RSG2.

#### I need to change how a framework call works (job system, older inventory)

gumApi is open source, and the framework calls are in its `serverData` folder (the VORP file is `serverData/serverCompVorp.lua`). Two examples from tickets:

- Housing assigns a job when a job house is bought. That goes through `setJob` in gumApi, which is where to look if you use a multijob table.
- For an older VORP inventory, the developer's advice was to adjust the structure in the VORP `serverData` file by hand.

## Settings added in updates

- **2026-11-02**. Cleaner design for inputs, answers, and other UI elements. Fixes for the new prompt system.
- **2026-10-26**. Updated files for better prompt interaction, plus fixes.
- **2026-10-20**. gumApi no longer requires you to select a framework. Added a language package for basic translations (`configLanguage.lua`). Added a new prompt system. Required by the upcoming Ecology, Farming, and Housing updates.
- **2026-10-16**. Compatibility with the JRS framework.
- **2026-08-11**, client/js. New commands: `/cc` copies your character's X, Y, Z coordinates, `/cch` copies X, Y, Z, H (heading).
- **2026-08-06**. Metadata update in `serverData`.
- **2026-07-12**. Metadata support. Only the framework's `serverData` needs updating.
- **2026-06-15**. VORP side: updated the server side part of `canCarry`.
- **2026-06-13**, css. Inputs always display above all windows.
- **2026-05-31**. Long text in inputs, new visual design, poly points drawn as walls (used by housing), optimized prompt handling, notifications with or without an icon, ESC closes inputs. Update your scripts from portal.cfx along with it.
- **2025-04-23**. Updated the server side weapon give for stores.
- **2025-02-18**, `clientData/client.lua`. `getAnswer` can be closed with ESC. For storage updates on VORP in gum_housing, replace the VORP file in `serverData`.
- **2024-11-18**. RSG 2.0 compatibility. Change `"RSG"` to `"RSG2"` in the config.
- **2024-10-26**, `Client.lua`, `index.js`. Input handling adjusted. Unregistered prompts no longer cause error messages.

*Built from the changelog and resolved support tickets. For anything not covered here, open a ticket in the GUM Scripts Discord.*
