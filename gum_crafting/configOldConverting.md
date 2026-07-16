# Converting Old Configuration

If you already have existing crafting recipes from an older version of **gum_craft**, you can easily convert them to the new crafting structure using **CraftingHelper**.

The converter will automatically transform your old configuration into the new format, so you don't need to recreate all recipes manually.

---

## Step 1 - Copy Your Old Configuration

Open your old crafting configuration file and copy the complete content.

Copy the whole configuration that contains your existing crafting recipes.

---

## Step 2 - Paste Into CraftingHelper

Open:

```
CraftingHelper/config.lua
```

Replace the existing content with your old configuration.

Save the file.

---

## Step 3 - Run Converter

Start your server and enter the game.

Open **CraftingHelper** and type:

```
/convert
```

The converter will automatically process your old configuration and create the new crafting structure.

---

## Step 4 - Load New Configuration

After the conversion is complete:

1. Restart the CraftingHelper resource.
2. Open CraftingHelper again:

```
/craftingHelper
```

You should now see your converted recipes in the new format.

---

## Step 5 - Export To gum_craft

The final step is the same as creating new recipes.

Click:

```
Copy All Book Data
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