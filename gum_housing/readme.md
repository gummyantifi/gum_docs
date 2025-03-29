
## gum_housing 

### 📌 Initial Installation & Configuration  

1️⃣ **Translate the script**  
   - Open the file: `config/configLanguage.lua`  
   - Adjust the language settings as needed  

2️⃣ **Enable Debug Mode**  
   - Before testing, set `debug = true`  
   - This helps load script settings dynamically **without restarting the game after each change**  

⚠️ **Important:** When deploying the script on a live server, **disable debug mode** to prevent issues!  



### Teleporting between Houses
- To teleport between houses, move out of the current house's range.
- Use the teleport function to move to the desired house.






### **Custom House Position Creation**
1. Enable Config.Debug in the script settings.
2. In-game, use the command "/createHouse ModelName".
3. Position the house objects as desired.
4. Press Enter or Backspace to save the coordinates to the clipboard.

### **Adding Doors and Windows to a Custom House**
1. With Config.Debug enabled, use the command "/createHouse ModelName" in-game to begin creating a custom house.
2. Place doors and windows in the desired locations.
3. Use the command "/createPosition ModelName 1" to mark the position of each door and window.
4. Repeat the process for additional positions as needed.
5. Press Backspace to save all the information to the clipboard. You can then paste it (Ctrl+V) to easily add doors and windows.
For a visual guide, refer to the following video: [Script Usage Video Guide](https://youtu.be/QnCAQtMmEM8)

6. Via Interior_Book you can build





### **Create interior for teleport**
1. "/createInterior MODELNAME"
2. Move your character to doors
3. You can find offset information in the console under the F8 key for ConfigInteriorPreset.lua
4. Via Interior_Book you can build




### **Create exist house ** (i mean, opened interior at game)
Via Build_Book press build at first page where is "?"

## ** Land 
Via Build_Book press build at last page where is "?"

