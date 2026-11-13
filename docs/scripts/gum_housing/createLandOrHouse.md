# Creating Houses and Interiors from Presets in the Game

## Simplest Part:

1) Open the book (by default, it's the item "Build_Book"). The item name is set by `Config.Build_Book` in `config/config.lua`.

Your job needs two permissions in `Config.Jobs`: `openBook = true` to open the book, and `buildViaBook = true` to use the Build button. Without `buildViaBook` you can look through the book, and Build answers "You are not allowed use build house."

### Description:

- The first page with the "?" icon is for creating a house already present in the game, such as MLO (Map Loading Objects), Sheriff's Station, and others. This is used to register these types of houses. It is the `existHouse` preset. No model is spawned for it, you only mark the plot.

- Because the first page spawns no model, it also gives you a plot with no house on it. The config in the current source has no separate land page: the `["Empty"]` entry at the end of `config/configHousePreset.lua` is commented out, and an `Empty` page is drawn as a blank page with no Build button.

- The other pages are houses that are included in the presets (`Config.Presets` in `config/configHousePreset.lua`).

---

### Steps:

1. Select the desired house or land by opening the book and selecting the appropriate page.
   
2. Choose one option and click the "Build" button.
   
3. For a preset house, a see-through preview of the house appears in front of you. Place it using the keys on the left side of the screen: `R` and `F` for up and down, the arrow keys for X and Y, `Q` and `E` to rotate. Click the left mouse button to save the position. This step is skipped on the first page, because no model is spawned.
   
4. Now define the polygonal plot of land. Stand in the spot where the plot should begin and click the left mouse button, then walk the perimeter and left-click at each corner to mark it.

5. Right-click to finish the plot, then answer the setup questions:
   - "Dou you want fixed price?": Yes for a house that is bought, No for a house that is rented.
   - Blip yes or no, base storage size, price (or rent), tax in percent (fixed price only), Z limit (`0` turns it off), normal or job house, dollar or gold, and a blip sprite (`0` keeps the default).
   - For a job house, the job name.

   Once done, the house or land is saved and ready for construction. Closing the setup window cancels the whole thing.

---

### The Same Process for Interiors:

Interiors use the second book (by default the item "Interior_Book", set by `Config.Interior_Book`). The process is identical, with only these differences:

- Before pressing the "BUILD" button, **YOU MUST BE STANDING AT THE LOCATION WHERE THE TELEPORT TO THE INTERIOR WILL BE**. Your position and heading at that moment are saved as the entrance.

- There is no model to place. After Build you go straight to marking the plot with left-click and finishing with right-click.

- The setup questions have no Z limit.

- The interior book only checks `openBook`. It does not check `buildViaBook`.
