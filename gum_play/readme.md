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

4. **Start Script**
   - Start the script after GumApi is running.

5. **Configure and Enjoy**
   - Modify the configuration file according to your preferences.
   - Play around with the script and enjoy its features.

**WARNING:** Some scripts require server-side startup and cannot be used while you are in the game. Make sure to start the script before joining the game to load all the necessary information from the SQL database.

Feel free to reach out if you have any questions or need further assistance. Enjoy using the script!



**Setup:**
For setup on chair you need you this :
```
				TriggerEvent("gum_play:sitOnChair", true)
```

for cancel :

```
				TriggerEvent("gum_play:sitOnChair", false)
```