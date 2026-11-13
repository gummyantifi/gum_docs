# Converting Old Configuration

If you already have existing crafting recipes from an older version of **gum_craft**, you can easily convert them to the new crafting structure using **CraftingHelper**.

The converter will automatically transform your old configuration into the new format, so you don't need to recreate all recipes manually.

Before you start, back up your old config and `craftingHelper/data.json`. The converter overwrites `data.json`, so books you saved in CraftingHelper before are replaced.

---

## Step 1 - Copy Your Old Configuration

Open your old crafting configuration file and copy the complete content.

Copy the whole configuration that contains your existing crafting recipes.

---

## Step 2 - Paste Into CraftingHelper

Open:

```
craftingHelper/config.lua
```

Replace the existing content with your old configuration.

The converter reads the `Config.Items` table, so the file has to start with `Config = {}` and define `Config.Items`.

Save the file.

---

## Step 3 - Run Converter

Start your server (or restart the craftingHelper resource, so it loads the new `config.lua`).

Run the command:

```
/convert
```

You can type it in the game chat or in the server console (as `convert`). CraftingHelper does not have to be open, and the command prints no confirmation.

The converter will automatically process your old configuration and create the new crafting structure in `craftingHelper/data.json`.

---

## Step 4 - Load New Configuration

After the conversion is complete:

1. Restart the CraftingHelper resource.
2. Open CraftingHelper again:

```
/craftingHelper
```

You should now see your converted recipes in the new format.

The converter copies the book settings (name, item, jobs, command, coords) and, for each recipe: item, label, count, time, description, required items, weapon, grade, profession, profession exp, hide recipe, category, category image, props and returned items. Metadata and discovery are not copied. Add them in CraftingHelper or by hand.

Props are copied as they are. Check that each prop entry has the `name`, `hash` and `id` fields the new version uses (see [Manual Crafting Configuration](configWithoutHelper.md)).

---

## Step 5 - Export To gum_craft

The final step is the same as creating new recipes.

Click:

```
Copy all book data
```

This will copy the complete new crafting configuration.

Open:

```
gum_craft/configRecipes.lua
```

Replace the old recipes:

```
CTRL + A
```

Paste the new configuration:

```
CTRL + V
```

Restart **gum_craft**.

Your old crafting system is now converted and ready to use with the new version.
