# GUM Stables: install and configure

`gum_stables` is a **comprehensive script** with a **wide range of customizable settings**.  
All configuration options and functions can be found in the **configuration file**.  

---

### ✨ Initial Installation & Configuration  

1️⃣ **Check SQL Tables**  
- Many users make the mistake of trying to run the script when they have previously used stables from a framework. To avoid this issue, **check your database tables**:  
  - Ensure that the tables `horses` and `wagons` do not already exist in your database.  
  - If they exist, `right-click` on the table and select `Drop` to remove them before proceeding.  

2️⃣ **Translate the script**  
   - Open the file: `cfg/configLang.lua`  
   - Adjust the language settings as needed  

3️⃣ **Enable Debug Mode**  
   - Before testing, set `debug = true`  
   - This helps load script settings dynamically **without restarting the game after each change**  

⚠️ **Important:** When deploying the script on a live server, **disable debug mode** to prevent issues!  

---

### 🔄 Despawning Horses & Carts  

If you need to remove a horse (e.g., when entering a solo session), use these events:  

```lua
TriggerEvent("gum_stables:fleeHorseInstant")  
TriggerEvent("gum_stables:fleeCartInstant")  
```

