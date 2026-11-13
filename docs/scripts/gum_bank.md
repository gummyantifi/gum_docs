---
icon: building-columns
---

# GUM Bank

GUM Bank is a banking system for RedM. You can run it as simple automatic banks, or turn on bankers, loans, savings, investments, checks, and banks that operate on their own funds.

## At a glance

- Video: [GUM Banks on YouTube](https://youtu.be/7vEvIAFU3OE)

## What it does

### Accounts and vaults

- Players can create multiple bank accounts.
- Players can have personal storage vaults with configurable limits.
- You set the account creation fee and the vault price based on storage size.
- Accounts can be shared with other players.
- Every account keeps a full transaction history: deposits, withdrawals, transfers, payments, shared account activity, and other important banking interactions.

### Local or global banking

- Accounts can be reachable from every bank, or restricted to the bank where they were created.
- The same choice applies to account visibility.
- Example: an account created in Valentine can allow withdrawals only in Valentine, or you can enable global access across all banks.

### Deposits, withdrawals, and transfers

- Minimum deposit amount.
- Minimum withdrawal amount.
- Minimum and maximum transfer limits, with separate limits for personal accounts and player transfers.
- Percentage-based fees for deposits, withdrawals, money transfers, and gold transactions.

### Savings

- You configure the minimum required deposit, the maximum savings balance, the interest interval, and the interest rates.
- Example: every 23 hours, players receive interest based on their savings balance.
- Smaller savings can receive a higher rate and larger balances a lower rate.

### Loans

- You configure minimum and maximum loan amounts, repayment duration, interest rates, the maximum number of active loans per player, and the maximum number of missed payments.
- Unpaid installments accumulate automatically.
- When a player reaches the missed payment limit, the bank can automatically block withdrawals, deposits, and transfers, disable savings, and prevent new loans.
- Access is restored automatically once the debt is paid.

### Investments

- An investment market with prices that change over time. The sales description gives gold, oil, companies, resources, and custom investments as examples.
- Each investment is configured on its own: starting value, minimum and maximum price, volatility, trend, crash chance, complete market crashes, risk level, profit percentage, price update interval, and maximum owned units.

### Banker job

Bankers can:

- Create bank accounts and loans.
- View customer accounts and transaction history.
- Manage banking fees.
- Exchange gold for dollars.
- Configure bank taxes and services.
- Block and unblock bank accounts.

Player account creation and player loan creation can be turned off completely, so only bankers handle them.

### Checkbooks

- Players can create personalized checks, write them for other players, and set a custom amount.
- Checks run on item metadata. You need only one Check item and one Checkbook item, with no separate items for different check types.

### Bank operating modes

- **Standard Bank:** fully automatic, no bankers.
- **Managed Bank:** bankers manage fees, taxes, and services, and the bank receives a configurable percentage from transactions.
- **Independent Economy:** each bank operates with its own funds. Players can withdraw only if the bank has enough balance, and transaction fees are added to the bank's available funds.

## Before you install

Install **gumApi** first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).

- The checkbook system needs one Check item and one Checkbook item.

## Configuration notes

### Opening hours

Added 2026-07-18, in the bank configuration:

```lua
openCloseHours = {open = 6, close = 20}, -- Bank open and close hours (24-hour format)
```

The matching translation line goes in the `prompt` section of `ConfigLang`:

```lua
showOpenedHours = "Bank is closed. Opening hours: %s:00 - %s:00",
```

### Storage without a job

Added 2026-08-01, in `Config.BankCoords` under `baseSetting`:

```lua
createStorage = true, -- if true, players can create storage in storage spot
```

With this on, a player can create storage without needing a job. The storage is for that player only.

### Savings cap

Added 2026-08-01, in `Config.SavingSettings`:

```lua
MaxSavingInterestCap = 999999, -- Maximum interest cap for saving account (false = no limit)
```

The savings system was reworked in this update. You now need to set either a maximum limit or no limit at all. Example from the changelog: a player who reaches the previous maximum of `99,999` can keep saving up to `999,999` with `MaxSavingInterestCap = 999999`.

### Translation lines for unblocking accounts

Added 2026-07-18 in `ConfigLang`.

In `notifications`, add:

```lua
accountUnblockedSuccessfully = "Account unblocked successfully.",
uHaveBlockedAccountByBanker = "You have blocked account #%s as a banker.",
```

In `inputs`, remove:

```lua
uHaveBlockedAccountByBanker = "You have blocked account #%s as a banker.",
```

In `inputs`, add:

```lua
doYouWantUnblockAccount = "Do you want to unblock bank account #%s?",
```

In `bankMenu`, add:

```lua
unblockAccount = "Unblock Account",
```

## Common problems and fixes

### A bank account stays locked because of unpaid loans

Accounts blocked for unpaid loans could not be unlocked. This was fixed on 2026-08-01. After updating, unlocking works and the money owed is taken directly from the player's inventory.

### On VORP, the check item stays in the inventory after it is put into the bank

This was a server-side bug. It was fixed on 2026-08-02. Update the server files.

### An account created by a banker does not work properly

Accounts created through the banker job were not stored in the server cache, and several required arguments were missing. This was fixed on 2026-07-18. Update to that version or later.

### The amount written in words on a check is wrong

The number-to-text conversion on checkbooks was fixed on 2026-07-18. Update to that version or later.

## Recent changes

- **2026-08-02:** Fixed on VORP: the check item is now removed after it is put into the bank. Server files changed.
- **2026-08-01:** Fixed accounts that could not be unlocked because of unpaid loans. Added `createStorage` so players can create storage without a job. Reworked savings and added `MaxSavingInterestCap`. Client, server, and JS files changed. Add the two new settings to your config.
- **2026-07-18:** Added bank opening hours with `openCloseHours`, plus the `showOpenedHours` line in the `prompt` section of `ConfigLang`. Client, server, and JS files changed.
- **2026-07-18:** Bankers can now unblock bank accounts. Fixed banker-created accounts not being stored in the server cache, the number-to-text conversion on checkbooks, incorrect account history text, button text that did not fit in the list, and the loan slider size in the banker menu. CSS, JS, client, and server files changed. Update `ConfigLang` with the lines listed above.

*This page was built from the script's announcement, its changelog, and resolved support tickets. For anything not covered here, open a ticket in the GUM Scripts Discord.*
