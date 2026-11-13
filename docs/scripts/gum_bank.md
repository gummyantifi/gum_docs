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
- Every account keeps a transaction history: deposits, withdrawals, transfers, payments, shared account activity, and other banking interactions. The number of records kept per account is configurable.

### Local or global banking

- Accounts can be reachable from every bank, or restricted to the bank where they were created.
- The same choice applies to account visibility.
- Example: an account created in Valentine can allow withdrawals only in Valentine, or you can enable global access across all banks.

### Deposits, withdrawals, and transfers

- Minimum deposit amount.
- Minimum withdrawal amount.
- Minimum and maximum transfer amount.
- Percentage-based fees for deposits, withdrawals, money transfers, and gold transactions.

### Savings

- You configure the minimum and maximum deposit, the interest interval, the interest rates, and a cap on earned interest.
- Example: every 23 hours, players receive interest based on their savings balance.
- Smaller savings can receive a higher rate and larger balances a lower rate.

### Loans

- You configure loan types with minimum and maximum amounts, repayment duration, and interest rates, plus the maximum number of active loans per player and the maximum number of missed payments.
- Each missed daily repayment is counted on the account.
- When an account passes the missed payment limit, the bank can block withdrawals, deposits, and transfers, disable savings, and prevent new loans.
- The player restores access by paying the missed repayments.

### Investments

- An investment market with prices that change over time. The default config ships gold, emeralds, and real estate, and you can add your own.
- Each investment is configured on its own: starting price, minimum and maximum price, volatility, trend, crash chance, complete market crash chance, risk level, and profit percentage.
- The price update interval and the limits on owned units are set once for the whole market.

### Banker job

Bankers can:

- Create bank accounts and loans.
- View customer accounts and transaction history.
- Manage banking fees.
- Exchange gold for dollars.
- Configure bank taxes and services.
- Block and unblock bank accounts.

Player account creation and player loan creation can be turned off per bank, so only bankers handle them.

### Checkbooks

- Players can buy checkbooks at the bank, write a check for another player, and set a custom amount.
- Checks run on item metadata. You need only one Check item and one Checkbook item, with no separate items for different check types.

### Bank operating modes

- Standard Bank: fully automatic, no bankers.
- Managed Bank: bankers manage fees, taxes, and services, and the bank collects the transaction fees.
- Independent Economy: each bank operates with its own funds. Players can withdraw only if the bank has enough balance, and transaction fees are added to the bank's available funds.

## Before you install

Install gumApi first and place it between your framework and the script in your resource order. See [Install gumApi](../start/gumapi.md).

The script also calls `oxmysql` directly, so `oxmysql` must be running before it.

## Install

1. Put the `gum_bank` folder in your resources and start it after gumApi and `oxmysql`.
2. Add two items to your inventory items. The names come from `Config.BankCheck` in `config.lua`:
   - `Bank_Checks`: the checkbook. Using it opens the check writing form.
   - `Bank_Check`: a written check. It carries the recipient, amount, date, and writer as metadata.
3. Start the server. There is no SQL file to import. On start the script creates its own tables if they are missing: `bankdata`, `bankdata_invest`, `bankdata_setting`, `bankdata_storages`, and `gum_bank_housing_receipts`.

Account ids and storage ids start at 1000.

## Configuration

Two files are editable: `config.lua` and `configLang.lua`.

### General: `Config.BankSetting`

| Key | Default | Meaning |
| --- | --- | --- |
| `MaxAccounts` | `3` | Accounts one character can own. |
| `AccountCreationPrice` | `0.5` | Fee to open an account. |
| `AccountCreationName`, `StorageCreationName` | Czech text | Default name of a new account or storage. Change these to your language. |
| `HistoryRecords` | `40` | History entries kept per account. |
| `AutoSaveInterval` | `5` | Minutes between saves to the database. |
| `VisibilityAccounts` | `"global"` | `"global"`: a player sees all their accounts in every bank. `"local"`: only the accounts opened in that bank. |
| `UsageAccounts` | `"local"` | `"local"`: deposits and withdrawals work only in the bank where the account was opened. `"global"`: they work in every bank. |

`Config.BankSetting["Money"]` holds `MinWithdraw`, `MinDeposit`, `MinTransfer` (`1`), `MaxTransfer` (`1000`), `TransferTime` (`6000` seconds), `TransferFeePct` (`5`), `WithdrawFeePct` (`3`), and `DepositFeePct` (`3`). A transfer is not instant. It arrives after `TransferTime`.

`Config.BankSetting["Gold"]` holds `MinWithdraw`, `MinDeposit`, `WithdrawFeePct`, and `DepositFeePct`.

### Checks: `Config.BankCheck`

| Key | Default | Meaning |
| --- | --- | --- |
| `bankCheckBook` | `"Bank_Checks"` | Item name of the checkbook. |
| `bankCheckCheck` | `"Bank_Check"` | Item name of a written check. |
| `priceForCheckItem` | `0.5` | Price of one checkbook item at the bank. |
| `removeAfterUse` | `true` | `true`: the bank asks how many checkbook items to buy. `false`: each purchase buys one. |

Writing a check uses up one `Bank_Checks` item and gives the writer one `Bank_Check` item. To cash a check, both the writer and the person cashing it need an account marked as their main account. The money moves from the writer's main account to the casher's main account.

### Savings: `Config.SavingSettings`

| Key | Default | Meaning |
| --- | --- | --- |
| `MinSavingMoneyLimit` | `10` | Smallest savings deposit. |
| `MaxSavingMoneyLimit` | `5000` | Largest savings deposit. |
| `MaxSavingAccountsPerPlayer` | `1` | Savings accounts per player. |
| `WithdrawTime` | 24 hours | Seconds before a new saving can be withdrawn. |
| `InterestInterval` | 23 hours | Seconds between interest payments. |
| `InterestTiers` | 4 tiers | List of `{ max, rate }`. The rate of the first tier whose `max` covers the balance is used. |
| `MaxSavingInterestCap` | `999999` | Upper limit for the interest a saving can accumulate. Set `false` for no limit. |

`MaxSavingInterestCap` must be present. Use a number or `false`.

### Loans: `Config.LoanSettings`

- `MaxActiveLoanPerPlayer` (`3`) and `MaxMissedPayments` (`3`).
- `BannedAccount` decides what a blocked account loses: `BlockWithdraw`, `BlockDeposit`, `BlockTransfer`, `BlockLoan`, `BlockSaving`.
- `LoanTypes` lists the loans players can take. The default has `shortterm` and `longterm`. Each has `name`, `minPrice`, `maxPrice`, `minDuration`, `maxDuration` (days), `minInterestPct`, and `maxInterestPct`.

Repayments are daily and are taken from the account balance. If the balance is too low, the account's missed payment count goes up by one.

### Investments: `Config.BankInvestment`

- `UpdateInterval` (`5*60` seconds): how often prices move.
- `SaveInterval` (`10`): how many update cycles pass between saves.
- `MaxBuyedUnitsPerOneInvestment` (`3`) and `TotalBuyedUnitsPerPlayer` (`10`).
- `Investments`: one entry per investment with `name`, `price`, `min`, `max`, `baseMaxJump`, `baseVol`, `baseTrend`, `crashChance`, `completeCrashChance`, `risk`, `profitPct`, and `color`. On a complete crash, players lose their units in that investment.

### Banks: `Config.BankCoords`

Each entry is one bank. The default config ships Valentine, Blackwater, SaintDenis, Rhodes, Strawberry, and Armadillo.

| Key | Meaning |
| --- | --- |
| `name`, `sName` | Bank name and storage name, used on blips and prompts. |
| `openSpot` | Where players open the bank menu. |
| `bankManage` | Optional. Where a player with a banker job opens bank management. |
| `storageSpot` | Where players open the storage menu. |
| `npcSpot` | Ped model and position of the bank NPC. |
| `selfBankMoney` | `false`, `"tax"`, or `"all"`. See below. |
| `openCloseHours` | `{open = 6, close = 20}` in 24-hour format, or `nil` for always open. |
| `baseSetting` | What players can do at this bank. |
| `jobSetting` | What each banker job can do at this bank, keyed by job name. |
| `storageCapacity` | Storage tiers as `{capacity, price}`. The price is the weekly rent. |

`selfBankMoney`:

- `false`: Standard Bank. The bank holds no funds of its own.
- `"tax"`: Managed Bank. Deposit, withdrawal, and transfer fees go into the bank's funds.
- `"all"`: Independent Economy. Deposits, fees, account creation fees, and loan payoffs add to the bank's funds. Withdrawals and loans are paid from them and are refused when the bank does not hold enough.

`baseSetting` keys: `goldEnabled`, `dollarEnabled`, `sharingEnabled`, `transferEnabled`, `removeAccEnabled`, `createStorage`, `createAccount`, `useLoan`, `useInvestment`, `useSaving`, `buyCheckBook`.

`jobSetting` keys per job: `createLoan`, `createAccounts`, `viewAccounts`, `manageTaxes`, `createStorage`, `viewStorages`, `removeStorage`. The default jobs are named `Mayor_VL`, `Mayor_BW`, and so on. Replace them with your own job names.

### Opening hours

Set `openCloseHours` on a bank to close it at night. Outside those hours the NPC is gone and the prompt shows the `showOpenedHours` text from `configLang.lua`:

```lua
showOpenedHours = 'Bank is closed. Open hours: %s:00 - %s:00',
```

### Storage without a job

With `createStorage = true` in a bank's `baseSetting`, a player can create a storage at the storage spot without a banker. In the default config only Valentine has this key. Add it to the other banks if you want the same there.

Storage rent is weekly and is paid from the storage's prepaid balance. A storage with unpaid rent is locked until the owner prepays again. A player can prepay up to four weeks.

### Translations: `configLang.lua`

All text is in the `Config.Language` table in `configLang.lua`, split into `notifications`, `inputs`, `prompt`, `storageMenu`, `checkBank`, and `bankMenu`. If you are moving an old language file forward, make sure these keys exist:

- `notifications.accountUnblockedSuccessfully`
- `notifications.uHaveBlockedAccountByBanker`
- `inputs.doYouWantUnblockAccount`
- `prompt.showOpenedHours`
- `bankMenu.unblockAccount`

## Exports

These server exports exist for the GUM housing script and only answer when the calling resource is named `gum_houses`: `housingAccount`, `housingCredit`, and `housingRegionCredit`. They are not a general API for other scripts.

## Customers also ran into these

### A bank account stays locked because of unpaid loans

The player unlocks it from the bank menu by paying the missed repayments. The amount is taken from the cash the player carries, so the player needs more cash on hand than the amount owed. A banker with access to accounts can also unblock an account.

### On VORP, the check item stays in the inventory after it is put into the bank

This was a server-side bug in an older version. Update the server files.

### An account created by a banker does not work properly

Older versions did not store banker-created accounts in the server cache. Update the script.

### The amount written in words on a check is wrong

The number-to-text conversion on checks was fixed in an update. Update the script, including the files in `html/`.
