# CraftingHelper

**CraftingHelper** is a simple tool designed to make creating recipes for **gum_craft** easier and faster.

Instead of manually writing large configuration files, CraftingHelper automatically generates the correct recipe structure for you.

CraftingHelper is its own resource. Keep the folder name `craftingHelper`, start it after `gumApi`, and open it in game with:

```
/craftingHelper
```

CraftingHelper is a setup tool. Run the resource only on a development server without players, and stop it when you are done.

---

## 📖 Creating a New Crafting Book

To create a new crafting book, simply click:

```
[+] Add Book
```

This will create a new empty crafting book:

```
A_newBook
```

Click it in the list to edit it. You can then customize it:

### Book Name

```
Name
```

Set the display name of your crafting book.

Example:

```
Drug Manufacturing
```

---

### Item ID

```
Item ID
```

Defines the item required to open the crafting book. When you save, the book is stored under this ID, and gum_craft registers that ID as the usable item.

Example:

```
Cocaine
```

### Command and Coords

```
Command
```

Type `true` or `false`. Do not leave it empty: an empty value is exported as `""`, and gum_craft treats that as `true`.

With `true` the book is part of the crafting command menu and the item is not used. Adding coordinates also turns off the item.

After that, simply configure the remaining options according to the structure provided by CraftingHelper.

Some fields add an entry to a list. Type the value and press ENTER. Click an entry in the list to remove it.

| Field | What to type |
| --- | --- |
| Jobs | job name |
| Coords | `x, y, z` |
| Profession | `Profession, level` |
| Recipe | `amount, itemID, label` |
| PropHash | `model, itemID, name` |
| GiveBack | `amount, itemID` |
| Metadata | `type, value` |

Use `[+] Add Recipe` to add a recipe to the selected book. `Remove Recipe` and `Remove Book` delete the selected one.

---

# ❔ Built-in Help System

CraftingHelper includes a built-in help system.

By clicking the:

```
?
```

button, you can see detailed information about each option and what every field does.

This allows you to easily understand the configuration without constantly checking the documentation.

In ItemID, Label, and Recipe (where you write amount, itemID, label) you can use @ whispering. Type `@` and part of an item name, then click a suggestion to fill the field. That helps you make recipes faster.

---

# 💾 Saving Your Work

Once you have finished creating your crafting books and recipes, click:

```
Save all
```

This will save your current CraftingHelper setup to `craftingHelper/data.json`. The window closes after saving.

Your saved configuration can be opened and edited again in the future with `/craftingHelper`.

---

# 📋 Exporting Recipes To gum_craft

When your crafting setup is ready, click:

```
Copy all book data
```

CraftingHelper will automatically copy the complete crafting configuration.

Now open:

```
gum_craft/configRecipes.lua
```

Replace the old configuration:

```
CTRL + A
```

and paste the new one:

```
CTRL + V
```

Your old recipes will now be replaced with the newly generated CraftingHelper configuration. Restart **gum_craft** to load it.

### Discovery needs a hand edit

CraftingHelper exports the Discovery and Self Discovery fields as two separate values, `["discovery"]` and `["selfDiscovery"]`. gum_craft reads one table instead. If you filled in either field, change that recipe in `configRecipes.lua` after pasting:

```lua
["discovery"] = { ["blueprint"] = "Heroin_Blueprint", ["self"] = true },
```

Recipes without discovery can stay as exported (`["discovery"] = false`). See [Manual Crafting Configuration](configWithoutHelper.md) for the full recipe structure.

---

# 🚀 Why Use CraftingHelper?

CraftingHelper makes managing large crafting systems much easier:

✅ No manual configuration writing  
✅ Automatic recipe structure generation  
✅ Built-in explanations for every option  
✅ Faster recipe creation  
✅ Easy export directly into gum_craft  

Create your crafting system faster and with fewer configuration mistakes.
