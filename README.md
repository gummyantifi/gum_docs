<p align="center">
  <img src="https://redwestrp.eu/videoNew.png" />
</p>

# GUM Scripts docs

Installation, configuration, and troubleshooting for GUM Scripts (RedM). The pages are Markdown under `docs/` and are laid out for GitBook.

**Always install gumApi first.** Start with [Install gumApi](docs/start/gumapi.md), or browse the [full table of contents](docs/SUMMARY.md).

## Put it on GitBook

`.gitbook.yaml` tells GitBook the pages are in `docs/`, the home page is `docs/index.md`, and the sidebar is `docs/SUMMARY.md`.

1. In GitBook, open the space, then **Configure**, then **GitHub Sync**.
2. Connect the GitHub account that owns this repo and pick this repo and the `main` branch.
3. For the first sync choose **GitHub to GitBook**, so the repo's pages fill the space.
4. Publish the space (**Share**, then **Publish to the web**). Readers need no account.

After that, a change pushed to `main` shows up on the site, and an edit made in GitBook is committed back to this repo.

## Adding or changing a page

- Edit the Markdown file under `docs/`.
- A new page also needs one line in `docs/SUMMARY.md`, or GitBook will not show it in the sidebar.
- Link between pages with relative paths that end in `.md`, such as `../start/gumapi.md`.

## Where the pages come from

- `docs/start/gumapi.md` and the guides in `docs/scripts/gum_crafting`, `gum_housing`, `gum_ranch`, and `gum_stables` are the original guides from this repo, moved into `docs/`.
- Every `troubleshooting.md` and every single-page script (`docs/scripts/gum_*.md`) was written from the GUM Scripts Discord: the script's announcement, the changelog channel, and closed support tickets. Nothing in them comes from the script code. Each page says so at the bottom.
- `docs/start/buying-and-support.md` restates the Discord's rules and ticket panel text.

No customer or staff names, ticket numbers, or transaction ids appear on any page.
