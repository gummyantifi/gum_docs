Here is the **README** file for **gumApi** in English:  

---

# gumApi  

gumApi is a bridge between scripts and your framework. Thanks to gumApi, you don't need to use different versions of scripts; instead, you only need gumApi. This ensures that scripts will run on a wide range of frameworks.  

## Installation  

First, you must correctly place **gumApi**. Many customers make mistakes in this step, making it one of the most frequently asked questions. To ensure a proper and smooth installation, you must place gumApi between the script and the framework.  

For example:  

```
vorp_core  
vorp_inventory  
vorp_etc..  
vorp_etc..  
gumApi  
gum_stables  
```  

This ensures that your framework loads first, **gumApi** retrieves information from the framework, and then the script itself loads properly.  

## Configuration  

Before using scripts, you need to configure gumApi.  

Open the configuration file **"config.lua"** in gumApi and modify the following part:  

```lua
Config.Framework = "GUM"
```  

Of course, set it according to the framework you are using. The available options are:  

- `"GUM"`  
- `"VORP"`  
- `"REDEM"`  
- `"REDEMREBOOT"`  
- `"QBR"`  
- `"RSG"`  
- `"RSG2"`  
- `"CUSTOM"` (for custom structures, etc.)  

Additionally, you can configure the `"Config.Language"` section to set your preferred language.  

---

Would you like to add anything else? 😊