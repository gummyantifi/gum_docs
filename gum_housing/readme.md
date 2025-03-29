### Installation Steps

To install and set up the script, follow these steps:

1. **Download API Script**
   - Go to the Discord and download the API script from the following location: [API Script](https://discord.com/channels/870659641879724104/1048999455157919744)

2. **Start GumApi**
   - Ensure you have GumApi installed on your server.
   - Add the following line to your server configuration file:
     ```
     ensure gumApi
     ```
   - Start GumApi before running any custom scripts.

3. **Download Script Assets**
   - Visit the FiveM website at [keymaster.fivem.net/assets](https://keymaster.fivem.net/assets).
   - Open the "Granded assets" section and download the script you want to use.


## Troubleshooting Tips
- If you encounter an error related to exporting "stables" and you don't have it, open the client.lua file and remove any lines referencing "gum_stables".

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
1. Via Build_Book press build at first page where is "?"




RSG CORE : 
Replace at client for clothing menu : 
   TriggerServerEvent("gum_housing:check_outfits")

With this : 
   TriggerEvent("rsg-clothes:OpenOutfits")
