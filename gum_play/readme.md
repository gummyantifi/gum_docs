### 📌 Initial Installation
For functional knife game, you must register the session in your script.  

At the moment when the player starts sitting (e.g., via a key press), the following should be placed:  

```
TriggerEvent("gum_play:sitOnChair", true)
```

And in case the sitting session is canceled:  

```
TriggerEvent("gum_play:sitOnChair", false)
```

(This allows the script to track whether the player is sitting or not and releases the mini-game.)