---
icon: plug
---

# Install gumApi

Always install gumApi before any GUM script. Download the current version from the **gumapi-and-docs** channel in the GUM Scripts Discord: [open the download post](https://discord.com/channels/870659641879724104/1048999455157919744/1341408039324090388).

## What is gumApi?

gumApi acts as a **bridge** between your scripts and the framework. Instead of using different script versions for various frameworks, you only need **gumApi**.  
This ensures that scripts run smoothly across a wide range of frameworks.  

---

## ⚙️ **Installation Guide**  

### 🔹 Correct Placement of **gumApi**  

One of the most common mistakes during installation is incorrect placement. To ensure a **proper setup**, you must place **gumApi** **between the framework and the script**.  

✅ **Example directory structure:**  

```
vorp_core  
vorp_inventory  
vorp_etc..  
vorp_etc..  
gumApi  
gum_stables  
```

This way, the framework loads first, gumApi retrieves the necessary data, and then your script initializes properly.  

---

## 🛠️ **Configuration**  

> **Changed on 2026-10-20.** The gumApi update of 2026-10-20 removed this step: gumApi no longer needs a framework selected, you start it and it works. The framework setting below applies only to gumApi versions older than that.

Before using scripts, you need to configure **gumApi**.  

1️⃣ Open **config.lua** in the gumApi folder.  
2️⃣ Modify the following setting according to your framework:  

```lua
Config.Framework = "GUM"
```

### 🏗️ **Supported Frameworks**  
- `"GUM"`  
- `"VORP"`  
- `"REDEM"`  
- `"REDEMREBOOT"`  
- `"QBR"`  
- `"RSG"`  
- `"RSG2"`  
- `"CUSTOM"` *(for custom structures)*  

Additionally, you can set the **language** in `"Config.Language"`.  

---

## ❓ Need Help?  

If you encounter any issues, feel free to ask for **support**. 🚀  

