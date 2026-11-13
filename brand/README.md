# GUM Scripts brand kit

The colors, type, and art the docs site uses, and where each one is set. Change a value here first, then change it in the place listed next to it, so this file stays the record.

Taken on 2026-11-13 from the Tebex store (gum-scripts.tebex.io) and the GUM Scripts Discord.

## Colors

| Name | Hex | Where it comes from | Used for |
|---|---|---|---|
| Gum Blue | `#3498DB` | The Gum role and every script role in Discord, and the mascot's hair | Links, buttons, the active sidebar item (dark mode) |
| Gum Blue, deep | `#2176AE` | Gum Blue darkened so it reads on white | Links and buttons in light mode |
| Sky | `#7DD8FF` | Highlights in the mascot's hair | Spare accent. Not used on the site yet |
| Navy | `#113366` | The mascot's hoodie | Spare dark accent. Not used on the site yet |
| Charcoal | `#161616` | Tebex store page background | The look the dark mode is matched to |
| Panel | `#0D0D0D` | Tebex store cards | Reference only |
| Line | `#2D2D2D` | Tebex store card borders | Reference only |
| Paper | `#DDDDDD` | Tebex store buttons | Reference only |
| White | `#FFFFFF` | Tebex store text | Text on dark |

One accent only. Everything colored on the site is Gum Blue; the rest is neutral.

## Type

| Role | Font | Where it comes from |
|---|---|---|
| Headings on the store | Marcellus | Tebex store |
| Body on the store | Roboto | Tebex store |
| Docs site | Inter (GitBook default) | Changing fonts is a paid GitBook feature, so the docs keep the default |

## Art

| What | File |
|---|---|
| Mascot logo, 300 x 300 | `https://dunb17ur4ymx4.cloudfront.net/webstore/logos/b5e94ac97fa9f8af8e77f045dd798085d6300ac2.png` |
| Discord server icon | Discord, Server Settings, Overview |
| Script cover art, 1400 x 1400 each | The package image of each script on Tebex. The home page cards load them straight from Tebex |

## What is set in GitBook

These live in the GitBook app, not in this repo. Open the site, then **Customize**, then **Theme**.

| Setting | Value |
|---|---|
| Theme | Clean |
| Primary color, dark mode | `#3498DB` |
| Primary color, light mode | `#2176AE` |
| Tint color | Primary (both modes) |
| Default mode | Dark |
| Show mode toggle | Off |
| Sidebar background | Filled |
| Sidebar list style | Pill |
| Everything else | Left at the default |

### Free and paid

Everything in the table above is on GitBook's free site plan. These are paid (Essential or Ultimate), so the docs do not use them: a custom logo in the header, the Bold and Gradient themes, semantic colors, code themes, fonts, icon style, the footer, the announcement banner, and a custom domain.

GitBook does not mark paid options while a site is on a paid plan or trial. Check **Settings**, then **Plan**, before changing anything outside the table.

## What is set in this repo

| What | Where | How to change it |
|---|---|---|
| Sidebar icon of a page | `icon:` at the top of the page's `.md` file | Any Font Awesome icon name, such as `horse` or `wrench` |
| Home page cards | `docs/README.md`, the two `<table data-view="cards">` blocks | One `<tr>` per card: title, link to the page, link to the cover image |
| Cover art on a card | The last link in the card's `<tr>` | Paste the new image address from Tebex (right click the package image, copy image address) |
| Yellow "install gumApi first" box | `{% hint style="warning" %}` in `docs/README.md` | Styles: `info`, `success`, `warning`, `danger` |
| Store and Discord buttons | The two `class="button"` links in `docs/README.md` | Change the `href` |

A new script needs: its page under `docs/scripts/`, one line in `docs/SUMMARY.md`, and one `<tr>` in the cards on the home page.

If a script's image is replaced on Tebex, its address changes and the card shows no cover until the new address is pasted in.
