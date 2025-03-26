# gumApi

gumApi is a bridge between scripts and your framework. With gumApi, you don't need to use different versions of scripts; you only need gumApi. This ensures that scripts run on a vast number of frameworks.

## Setup

Before using scripts, you must configure gumApi.

1. Open the configuration file `config.lua` in gumApi.
2. Modify the following line according to your framework:
   ```lua
   Config.Framework = "GUM"
   ```
   Available preset options:
   - `GUM`
   - `VORP`
   - `REDEM`
   - `REDEMREBOOT`
   - `QBR`
   - `RSG`
   - `RSG2`
   - `CUSTOM` (for custom structures, etc.)

3. Additionally, you can set your preferred language by modifying:
   ```lua
   Config.Language = "your_language"
   ```

