# CraftingHelper

**CraftingHelper** is a simple tool designed to make creating recipes for **gum_craft** easier and faster.

Instead of manually writing large configuration files, CraftingHelper automatically generates the correct recipe structure for you.

---

## 📖 Creating a New Crafting Book

To create a new crafting book, simply click:

```
ADD Book
```

This will create a new empty crafting book:

```
A_newBook
```

You can then customize it:

### Book Name

```
name
```

Set the display name of your crafting book.

Example:

```
Drug Manufacturing
```

---

### Item ID

```
item_id
```

Defines the item required to open the crafting book.

Example:

```
Cocaine
```

After that, simply configure the remaining options according to the structure provided by CraftingHelper.

---

# ❔ Built-in Help System

CraftingHelper includes a built-in help system.

By clicking the:

```
?
```

button, you can see detailed information about each option and what every field does.

This allows you to easily understand the configuration without constantly checking the documentation.

---

# 💾 Saving Your Work

Once you have finished creating your crafting books and recipes, click:

```
Save All
```

This will save your current CraftingHelper setup.

Your saved configuration can be opened and edited again in the future.

---

# 📋 Exporting Recipes To gum_craft

When your crafting setup is ready, click:

```
Copy All Book Data
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

Your old recipes will now be replaced with the newly generated CraftingHelper configuration.

---

# 🚀 Why Use CraftingHelper?

CraftingHelper makes managing large crafting systems much easier:

✅ No manual configuration writing  
✅ Automatic recipe structure generation  
✅ Built-in explanations for every option  
✅ Faster recipe creation  
✅ Easy export directly into gum_craft  

Create your crafting system faster and with fewer configuration mistakes.