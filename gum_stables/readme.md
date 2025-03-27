
## gum_stables – Advanced Horse Management 🐎  

`gum_stables` is a **comprehensive script** with a **wide range of customizable settings**.  
All configuration options and functions can be found in the **configuration file**.  

---

### 📌 Initial Installation & Configuration  

1️⃣ **Translate the script**  
   - Open the file: `cfg/configLang.lua`  
   - Adjust the language settings as needed  

2️⃣ **Enable Debug Mode**  
   - Before testing, set `debug = true`  
   - This helps load script settings dynamically **without restarting the game after each change**  

⚠️ **Important:** When deploying the script on a live server, **disable debug mode** to prevent issues!  

---

### 🔄 Despawning Horses & Carts  

If you need to remove a horse (e.g., when entering a solo session), use these events:  

```lua
TriggerEvent("gum_stables:fleeHorseInstant")  
TriggerEvent("gum_stables:fleeCartInstant")  
