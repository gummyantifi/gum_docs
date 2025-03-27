## 📌 What is **gumApi**?  

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

