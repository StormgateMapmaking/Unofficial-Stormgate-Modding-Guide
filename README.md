# Unofficial Stormgate Modding Guide

> **Unofficial. Use at your own risk.**  
> Everything here is subject to break with future patches. A single typo can brick your map — **make backups/commit changes often!**

---

## Table of Contents

- [Overview](#overview)
- [Where Files Live](#where-files-live)
- [Data Editing](#data-editing)
  - [Modifying Existing Archetypes](#modifying-existing-archetypes)
  - [Creating New Archetypes](#creating-new-archetypes)
  - [Custom Ability](#custom-ability)
  - [Custom Terrain Tileset](#custom-terrain-tileset)
  - [Custom Terrain Layer](#custom-terrain-layer)
  - [Adding WorldPoints](#adding-worldpoints)
  - [Adding Regions](#adding-regions)
  - [Custom Resources](#custom-resources)
- [Trigger Editing](#trigger-editing)
  - [How Triggers Work](#how-triggers-work)
- [Stormgates](#stormgates)
  - [Adding Stormgates](#adding-stormgates)
  - [Stormgate Issues & Limitations](#stormgate-issues--limitations)
- [Editing Tips](#editing-tips)

---

## Overview

Stormgate maps contain several JSON files that define:

- all game objects (units, abilities, doodads, resources, etc.)
- map terrain and layout
- triggers and scripted behaviors

Most modding currently happens by manually editing these files. Since the editor doesn't (at time of writing) have a UI to cover this functionality — **this guide explains how to go deeper.**

## Where Files Live

Here’s where you’ll find the most important files for modding:

- `runtime_session.json`
  Generated only when you build your map. Contains every object and script in your map.

- `managed_archetypes.json`
  Holds all overrides or custom definitions for archetypes.  
  ➔ For new data, place individual JSON files in`<your_map>\catalog\archetypes\`

- `preplaced_named_objects.json`
  Defines specific objects placed on the map (e.g. units, destructibles).

- `terrain.json`
  Stores cliff heights, terrain cells, and water tiles.

- `map.json`
  Basic map metadata (name, tileset, dimensions).

- `catalog\archetypes\<trigger_name>.json`
  Each trigger is stored in its own JSON file.

- `AppData\Local\Stormgate\Saved\Maps\Script\public\`
  You’ll find all the script functions inside the files here.

## Data Editing

### Modifying Existing Archetypes

Instead of modifying the base game’s data, you can create an override:

#### Example "Lancer override"
```json
{
  "id": "Lancer",
  "vital_health": {
    "maximum": 999.0,
    "regen": 0.0,
    "starting": 999.0
  }
}
```

⚠️ Avoid overriding official data if you plan to keep maps working across patches; patches may significantly change how units and such are implemented.

### Creating New Archetypes

When you want to add a new unit or custom data, the best way is to create a brand new file inside `<your_map>\catalog\archetypes\`. This keeps your changes organized and makes it easier to manage updates or troubleshoot issues later.

Behold my completely original OC with its deep traumatic backstory and totally reasonable power level for a tier 1 unit — Dingus.

#### Example "Dingus"

```json
{
  "id": "Dingus",
  "based_on": "Lancer",
  "vital_health": {
    "maximum": 300.0
  }
}
```

### Custom Ability

If you want to add your own fully custom abilities, or maybe just make some adjustments to existing abilities, you can do that, as well.

Note that in this example, because we're changing an array, we need to instance all the values for that array item.

#### Example "AbyssalCharge on 2 second cooldown"

```json
{
  "commands": [
    {
      "charges": {
        "max_count": 0,
        "name": "default",
        "recharge_duration": 0,
        "starting_count": 0
      },
      "cooldown": {
        "duration": 2.0,
        "name": "_AbyssalCrushingCharge0",
        "scope": "Ability"
      },
      "cost_power": 0.0,
      "cost_resources": {
        "resource_a": 0.0,
        "resource_b": 0.0,
        "resource_c": 0.0,
        "resource_d": 0.0
      },
      "cost_vitals": {
        "energy": 0.0,
        "health": 0.0
      },
      "cost_vitals_percentage": {
        "energy": 0.0,
        "health": 0.0
      },
      "requirement": "NoRequirement"
    }
  ],
  "id": "_AbyssalCrushingCharge0",
}
```

### Custom Terrain Tileset

We can add custom terrain tilesets, and can specify their cliffs and terrain layers. This allows you to combine textures intend for other tilesets in new and unique ways to better express your creative vision for a map.

#### Example "Trenchfoot Tileset"

```json
{
    "__base_type": "TerrainTileset",
    "based_on": "TerrainTileset_TemplarCave",
    "cliff_set_a": "DA_Cliffset_Rand_A",
    "cliff_set_b": "DA_Cliffset_Rand_B",
    "display_name": "",
    "doodad_aggregate_layer": "Moss",
    "id": "TerrainTileset_Trenchfoot",
    "layer_1": "TerrainLayer_ForestAutumn_Sand",
    "layer_2": "TerrainLayer_Deadlands_CaveGround",
    "layer_3": "TerrainLayer_Deadlands_GravellySand",
    "layer_4": "TerrainLayer_ForestAutumn_RockyGround",
    "layer_5": "TerrainLayer_Deadlands_TemplarTiles",
    "layer_6": "TerrainLayer_Deadlands_TemplarTilesFlat",
    "layer_7": "TerrainLayer_Deadlands_TemplarTilesLarge",
    "layer_8": "TerrainLayer_ForestAutumn_Mud",
    "render_abyss": true,
    "water_table": "DA_WaterTable_ForestAutumn"
}
```

In most cases, you'll want to use an existing terrain layer. At time of writing, these are the known terrain layers.

#### Known Terrain Layers
```
TerrainLayer_Arctic_01
TerrainLayer_Arctic_02
TerrainLayer_Arctic_03
TerrainLayer_Arctic_04
TerrainLayer_Arctic_05
TerrainLayer_Arctic_06
TerrainLayer_Arctic_07
TerrainLayer_Arctic_08
TerrainLayer_Deadlands_BeachSand
TerrainLayer_Deadlands_CaveGround
TerrainLayer_Deadlands_FlatRock
TerrainLayer_Deadlands_GravellySand
TerrainLayer_Deadlands_GroudRocks
TerrainLayer_Deadlands_MuddySand
TerrainLayer_Deadlands_PorousRocks
TerrainLayer_Deadlands_RedSand
TerrainLayer_Deadlands_RoughSand
TerrainLayer_Deadlands_Salt
TerrainLayer_Deadlands_SandDunes
TerrainLayer_Deadlands_SandSmallRocks
TerrainLayer_Deadlands_SmallRocks
TerrainLayer_Deadlands_TemplarTiles
TerrainLayer_Deadlands_TemplarTilesFlat
TerrainLayer_Deadlands_TemplarTilesLarge
TerrainLayer_Deadlands_WetSand
TerrainLayer_Foliage
TerrainLayer_ForestAutumn_DarkGrass
TerrainLayer_ForestAutumn_Dirt
TerrainLayer_ForestAutumn_GrassGround
TerrainLayer_ForestAutumn_GrayRock
TerrainLayer_ForestAutumn_Mud
TerrainLayer_ForestAutumn_PatchyGrass
TerrainLayer_ForestAutumn_RockyGround
TerrainLayer_ForestAutumn_Sand
TerrainLayer_InfernalShroud
TerrainLayer_InfernalStructure_Detail
TerrainLayer_InfernalStructure_MetalGround
TerrainLayer_InfernalStructure_Symbols
TerrainLayer_Mayhem_Grass01
TerrainLayer_Mayhem_Grass02
TerrainLayer_Mayhem_Grass03
TerrainLayer_Mayhem_Redsand
TerrainLayer_Mayhem_Rocks
TerrainLayer_Mayhem_Sand
TerrainLayer_Mayhem_SmoothSand
TerrainLayer_SigmaArctic_Snow1
TerrainLayer_SigmaArctic_Snow2
TerrainLayer_SigmaArctic_Snow3
TerrainLayer_SigmaArctic_SnowCliff
TerrainLayer_SigmaArctic_SnowRocks
TerrainLayer_SigmaArctic_SnowRough
TerrainLayer_SigmaArctic_SnowStormy
TerrainLayer_SigmaStation_Concrete
TerrainLayer_SigmaStation_ConcreteTiles
TerrainLayer_SigmaStation_FloorTilesLarge
TerrainLayer_SigmaStation_FloorTilesSmall
TerrainLayer_SigmaStation_MetalGrates
TerrainLayer_SigmaStation_MetalicAsymTiles
TerrainLayer_SigmaStation_MetalicTileLarge
TerrainLayer_SigmaStation_MetalicTilesSmall
TerrainLayer_SigmaStation_Terrain_06
TerrainLayer_SigmaStation_Terrain_07
TerrainLayer_UrbanRuins_ConcreteTilesLarge
TerrainLayer_UrbanRuins_ConcreteTilesLargeDamaged
TerrainLayer_UrbanRuins_ConcreteTilesSmall
TerrainLayer_UrbanRuins_DeadGrass
TerrainLayer_UrbanRuins_Dirt
TerrainLayer_UrbanRuins_GreenGrass
TerrainLayer_UrbanRuins_LargeSteelTiles
TerrainLayer_UrbanRuins_MetalPanel
TerrainLayer_UrbanRuins_Military
TerrainLayer_UrbanRuins_MilitaryTilesConcrete_01
TerrainLayer_UrbanRuins_RedGrass
TerrainLayer_UrbanRuins_SteelTile02
TerrainLayer_UrbanRuins_SteelTiles01
TerrainLayer_UrbanRuins_SteelTiles03
```
### Custom Terrain Layer

You can add custom terrain layers. While the established terrain layers work well for most purposes, sometimes you might have need to change something, such as the kind of "foliage" painted doodads that can be generated for them.

#### Example "Trenchfoot Sand"

```json
{
  "__base_type": "TerrainLayer",
  "based_on": "TerrainLayer",
  "display_name": "",
  "id": "TerrainLayer_Trenchfoot_Sand",
  "layer_name": "Sand",
  "minimap_color": "A67950FF",
  "painted_doodads": [
    {
      "doodad": "PaintedDoodad_Hinterlands_SmallRock1",
      "influence": 1.0
    },
    {
      "doodad": "PaintedDoodad_Hinterlands_SmallRock2",
      "influence": 1.0
    },
    {
      "doodad": "PaintedDoodad_Hinterlands_SmallRock3",
      "influence": 1.0
    },
    {
      "doodad": "PaintedDoodad_Hinterlands_SmallRock4",
      "influence": 1.0
    },
    {
      "doodad": "PaintedDoodad_Hinterlands_SmallRock5",
      "influence": 1.0
    }
  ],
  "texture_base": "Texture2D'/Game/Pegasus/Art/Biomes/ForestAutumn/Terrain/T_ForestAutumn_Terrain_Sand_D.T_ForestAutumn_Terrain_Sand_D'",
  "texture_height": "Texture2D'/Game/Pegasus/Art/Biomes/ForestAutumn/Terrain/T_ForestAutumn_Terrain_Sand_H.T_ForestAutumn_Terrain_Sand_H'",
  "texture_normal": "Texture2D'/Game/Pegasus/Art/Biomes/ForestAutumn/Terrain/T_ForestAutumn_Terrain_Sand_N.T_ForestAutumn_Terrain_Sand_N'",
  "texture_orm": "Texture2D'/Game/Pegasus/Art/Biomes/ForestAutumn/Terrain/T_ForestAutumn_Terrain_Sand_ORM.T_ForestAutumn_Terrain_Sand_ORM'",
  "use_custom_minimap_color": false,
  "value_blend_type": 0.4,
  "value_brightness": 1.0,
  "value_hue_shift": 0.0,
  "value_metalness_multi": 1.0,
  "value_metalness_offset": 0.0,
  "value_normal_power": 2.5,
  "value_roughness_multi": 1.0,
  "value_roughness_offset": 0.0,
  "value_saturation": 1.0,
  "value_tiling": 2.2
}
```
### Adding WorldPoints

WorldPoints and Regions are typically used by triggers to do stuff at places. Presumably, the editor will have a World Point and Region tools to manage adding these, but at time of writing you'll have to add their archetypes and placement in the map separately — like customised units.

- The archetype json file should go in `<your_map>\catalog\archetypes` folder.
- The object should be added to the `preplaced_named_objects.json` file.

#### Example "YourAreHere WorldPoint Archetype"

```json
{
  "__base_type": "WorldPoint",
  "based_on": "WorldPointRed",
  "id": "_WorldPoint0",
  "placed_name": "YouAreHere",
  "unreal_actor": "BP_IGEPlaceables_WorldPointRed"
}
```

#### Example "YouAreHere WorldPoint Preplaced Named Object"

```json
"A3CE777A-4072-67D4-49FC-6481F292554A": {
    "facing": 0,
    "kind": "_WorldPoint0",
    "player": 0,
    "position": [
        907478,
        -857775
    ]
}
```

### Adding Regions

Just like WorldPoints, Regions are split into archetypes and objects placed in the map.

#### Example "WelcomeHome Region Archetype"

```json
{
  "__base_type": "RegionBase",
  "based_on": "Region",
  "color": {
    "b": 0,
    "g": 255,
    "r": 0
  },
  "id": "_Region0",
  "placed_name": "WelcomeHome",
  "shapes": [
    {
      "circle": {
        "radius": 40.0
      },
      "mode": "additive",
      "rectangle": {
        "height": 0.0,
        "rotation": 0,
        "width": 0.0
      },
      "translation": {
        "x": 0,
        "y": 0
      },
      "type": "circle"
    }
  ],
  "starting_snowtags": [],
  "unreal_actor": "BP_IGE_Placeables_Region"
}
```

#### Example "WelcomeHome Region Preplaced Named Object"

```json
"A3CE777A-4072-67D4-49FC-6481F292554A": {
  "facing": 527635746,
  "kind": "_Region0",
  "player": 0,
  "position": [
      -1194624,
      1078784
  ]
}
```

### Custom Resources

Drop this into your map and edit the value to whatever you please; this allows you to have modified luminite mine amounts without breaking collection arrays.

#### Example "CustomLuminite"
```json
{
  "__base_type": "ResourceData",
  "based_on": "ResourceData",
  "id": "CustomLuminite",
  "agitation_duration": 0.0,
  "auto_harvester_gather_amount_multiplier": 1.0,
  "resource_gather_amount_per_trip": 50.0,
  "child_nodes": {}
}
```

## Trigger Editing

### How Triggers Work

Triggers in Stormgate are **deeply nested JSON expression trees**:
Every function comes with its own `script_id` and a set of arguments. You’ll find all the script functions inside `AppData\Local\Stormgate\Saved\Maps\Script\public\`.

#### Example “Hello, world!”

```json
{
  "actions": [
    {
      "function_id": "UI_SendChatMessage",
      "guid": "FD43E7EF40434538F4D2279E60C81140",
      "parameters": [
        {
          "expressions": [
            {
              "function": {
                "function_id": "Text_ToText",
                "guid": "B6CF3C0B4CA030BCFAE9048D292629A7",
                "parameters": [
                  {
                    "expressions": [
                      {
                        "guid": "90024D4A43E8066F33024BA96423A2E2",
                        "type": "Value",
                        "value": {
                          "guid": "3CE17AAE42A56C9B45F2D98B531D66DA",
                          "type": {
                            "directive": "FunctionType",
                            "implements": "",
                            "inner_type": "",
                            "param_id": "",
                            "sub_directive": "None",
                            "tags": [
                              "to_text"
                            ],
                            "type": "String"
                          },
                          "value": "Hello, world!"
                        }
                      }
                    ],
                    "guid": "CD84CDA74BC316C44C06E09F709FA104",
                    "id": "value"
                  }
                ]
              },
              "guid": "2CD3872A4DD80F67EAD8D299ECCBA6DF",
              "type": "Function"
            }
          ],
          "guid": "1D25CD814B40DEC7E2FED293E196FAA5",
          "id": "message"
        },
        {
          "expressions": [
            {
              "function": {
                "function_id": "PlayerGroup_GetAllPlayers",
                "guid": "DDDCADC6468A887788B5B7BE61ACF82F"
              },
              "guid": "C928B19D4191BA1AD8B0C983ADA88293",
              "type": "Function"
            }
          ],
          "guid": "592E50CD4DA157CC36EE49B025F51D00",
          "id": "group"
        }
      ]
    }
  ],
  "based_on": "Trigger",
  "events": [
    {
      "function_id": "Timer_OnTimeElapsed",
      "guid": "994D5B424522B96DB764EEB1F116CDDC",
      "parameters": [
        {
          "expressions": [
            {
              "guid": "4B281ECD4E27C558584D38863F95CF2D",
              "type": "Value",
              "value": {
                "guid": "8FFEC4024796B7611622688EC1A3AE64",
                "type": {
                  "directive": "FunctionType",
                  "implements": "",
                  "inner_type": "",
                  "param_id": "",
                  "sub_directive": "None",
                  "type": "Time"
                },
                "value": "1.0"
              }
            }
          ],
          "guid": "B76433A54480ACC5573E8C8686A7F78B",
          "id": "duration"
        }
      ]
    }
  ],
  "id": "HelloWorld"
}
```

**GUIDs** (Globally Unique Identifiers) ensure objects don’t conflict.
- Best practice is to just delete all GUID parameters; an entry not assigned a GUID will have one assigned to it automatically.
- But if you want/need one, you can generate a GUID using tools like https://www.uuidgenerator.net/guid.

## Stormgates

Stormgates are complex multi-phase objectives. As of now:

- Hard-coded for **5 stormgates** unless you rewrite the triggers.
- Changing the number of stormgates is possible, but extremely difficult.

### Adding Stormgates

Steps to add stormgates:

1. Add stormgates to `preplaced_named_objects.json`

```json
{
  "kind": "_StormgateObjectiveSubmerged4",
  "player": 31,
  "position": [ ... ],
  "facing": 0
}
```

2. Add unique stormgate entries to `managed_archetypes.json`

3. Place related archetype files and triggers into:
   ```
   <your_map>\catalog\archetypes\
   ```

### Stormgate Issues & Limitations

- Functions like:
  - `PrepareStormgateobjectiveLv1_Discoverable`
  - `PrepareStormgateObjectiveLv2_Discoverable`
- Hard-coded references to stormgate groups and player IDs.
- Known issue:
  - Player 1 and Player 2 on same team can be incorrectly flagged as enemies while capturing a stormgate.

## Editing Tips

Use a JSON-aware editor (e.g. VS Code).

⚠️ Watch for:

- missing commas
- trailing commas
- mismatched brackets

Save often and make incremental changes.

Errors in JSON syntax leads to crashing the game when attempting to test the map. Non-valid entries lead to the map not building (and not starting).
