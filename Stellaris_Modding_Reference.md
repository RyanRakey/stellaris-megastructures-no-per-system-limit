# Complete Stellaris Modding Reference Guide

> **Sources:** Stellaris Wiki, Stellaris Fandom Wiki, Paradox Forums, GitHub repositories (OldEnt triggers/effects list), Steam Community Guides, and Dev Diaries.
>
> **Last Updated:** April 2026

---

## Table of Contents

1. [Getting Started](#1-getting-started)
2. [Mod Structure & Descriptor Files](#2-mod-structure--descriptor-files)
3. [File Types & Formats](#3-file-types--formats)
4. [Core Scripting Language Syntax](#4-core-scripting-language-syntax)
5. [Scopes](#5-scopes)
6. [Triggers (Conditions)](#6-triggers-conditions)
7. [Effects](#7-effects)
8. [Modifiers](#8-modifiers)
9. [Events](#9-events)
10. [Variables & Dynamic Scripting](#10-variables--dynamic-scripting)
11. [Common Game Object Definitions](#11-common-game-object-definitions)
12. [Localization](#12-localization)
13. [Graphics & Interface](#13-graphics--interface)
14. [Debugging & Console Commands](#14-debugging--console-commands)
15. [Best Practices & Tools](#15-best-practices--tools)
16. [Key Resources](#16-key-resources)

---

## 1. Getting Started

Stellaris is built on the **Clausewitz Engine** and uses a proprietary scripting language. Unlike traditional programming languages (C, Java, Python), the syntax doesn't strictly separate basic syntax from game-specific structures.

**Key Principle:** Never modify base game files directly. Always create a mod and override/add files through the mod folder structure.

### Installation Paths

| Location | Path |
|----------|------|
| Game Files (Steam) | `C:\Program Files (x86)\Steam\steamapps\common\Stellaris\` |
| User Mod Folder | `%USERPROFILE%\Documents\Paradox Interactive\Stellaris\mod\` |
| Steam Workshop | `Steam\steamapps\workshop\content\281990\` |
| Logs & Documentation | `%USERPROFILE%\Documents\Paradox Interactive\Stellaris\logs\` |
| GFX Cache | `~\Documents\Paradox Interactive\Stellaris\<mod_name>\gfx` |

---

## 2. Mod Structure & Descriptor Files

Every mod requires **two descriptor files**:

### 2.1 `.mod` File (in `/mod/` folder)

This tells the launcher where to find the mod.

```
name="My Awesome Mod"
path="mod/my_awesome_mod"
supported_version="3.12.*"
tags={
    "Gameplay"
    "Events"
}
remote_file_id="1234567890"
```

### 2.2 `descriptor.mod` (inside mod folder)

Identical to the `.mod` file but **without** the `path` property.

```
name="My Awesome Mod"
supported_version="3.12.*"
tags={
    "Gameplay"
    "Events"
}
```

### Descriptor Properties

| Property | Required | Description |
|----------|----------|-------------|
| `name` | Yes | Display name in launcher/Workshop |
| `path` | Yes (in `.mod` only) | Path to mod folder (absolute or relative) |
| `supported_version` | Yes | Game version compatibility. Use `*` as wildcard (e.g., `3.12.*`) |
| `tags` | No | Array of Workshop tags |
| `remote_file_id` | No | Auto-set by Steam Workshop on upload |
| `dependencies` | No | Array of mod names that must load before this mod |

### Complete Folder Structure

```
%USERPROFILE%\Documents\Paradox Interactive\Stellaris\mod\
├── my_awesome_mod.mod          # Launcher descriptor
└── my_awesome_mod\              # Mod root folder
    ├── descriptor.mod           # Mod metadata
    ├── thumbnail.png            # Workshop thumbnail (max 1MB, .png)
    ├── common\                  # Core game data
    │   ├── agendas\
    │   ├── ambient_objects\
    │   ├── armies\
    │   ├── ascension_perks\
    │   ├── buildings\
    │   ├── component_sets\
    │   ├── component_templates\
    │   ├── country_types\
    │   ├── decisions\
    │   ├── deposits\
    │   ├── diplo_phrases\
    │   ├── economic_categories\
    │   ├── edicts\
    │   ├── ethics\
    │   ├── event_chains\
    │   ├── fallen_empires\
    │   ├── game_rules\
    │   ├── governments\
    │   │   ├── authorities\
    │   │   └── civics\
    │   ├── graphical_culture\
    │   ├── mandates\
    │   ├── megastructures\
    │   ├── modifiers\
    │   ├── name_lists\
    │   ├── observation_station_missions\
    │   ├── on_actions\
    │   ├── opinion_modifiers\
    │   ├── planet_classes\
    │   ├── planet_modifiers\
    │   ├── policies\
    │   ├── pop_faction_types\
    │   ├── precursor_civilizations\
    │   ├── random_names\
    │   ├── resolutions\
    │   ├── resources\
    │   ├── sector_types\
    │   ├── ship_sizes\
    │   ├── solar_system_initializers\
    │   ├── special_projects\
    │   ├── species_classes\
    │   ├── star_classes\
    │   ├── starbase_buildings\
    │   ├── starbase_modules\
    │   ├── starbase_types\
    │   ├── static_modifiers\
    │   ├── strategic_resources\
    │   ├── subjects\
    │   ├── technology\
    │   ├── terraform\
    │   ├── trade_conversions\
    │   ├── traditions\
    │   ├── traits\
    │   ├── war_goals\
    │   ├── scripted_effects\
    │   ├── scripted_triggers\
    │   ├── scripted_variables\
    │   ├── scripted_loc\
    │   ├── scripted_modifiers\
    │   ├── script_values\
    │   └── inline_scripts\
    ├── events\                   # Event definitions
    ├── decisions\                # Decision files (alternative path)
    ├── gfx\                      # Graphics & assets
    │   ├── interface\
    │   ├── models\
    │   ├── portraits\
    │   ├── event_pictures\
    │   └── ...
    ├── interface\                # UI definitions
    │   ├── *.gui                  # UI layout files
    │   └── *.gfx                  # Sprite definitions
    ├── localisation\             # Text strings
    │   └── english\
    │       └── mymod_l_english.yml
    ├── music\                    # Custom music (ogg format)
    ├── sound\                    # Sound effects
    └── prescripted_countries\   # Pre-made empires
```

---

## 3. File Types & Formats

| Extension | Purpose | Encoding |
|-----------|---------|----------|
| `.txt` | Game data scripts | UTF-8 |
| `.mod` / `descriptor.mod` | Mod metadata | UTF-8 |
| `.yml` | Localization strings | **UTF-8 with BOM** |
| `.dds` | Textures & icons | DirectDraw Surface |
| `.gfx` | Sprite/graphics definitions | UTF-8 (NOT UTF-8-BOM) |
| `.gui` | UI layout definitions | UTF-8 |
| `.asset` | 3D model/material assignments | UTF-8 |
| `.mesh` | 3D model files (binary) | Binary |
| `.ogg` | Audio files | Ogg Vorbis |
| `.lua` | Rare; some defines/overrides | UTF-8 |

---

## 4. Core Scripting Language Syntax

Stellaris scripting is **declarative**, not imperative in the traditional sense. It uses blocks and key-value pairs.

### 4.1 The `=` Operator

The scripting language uses `=` for **everything**:
- Boolean operations
- Assignments
- Scope changes
- Activating triggers and effects

```
# This is NOT traditional code. '=' is overloaded.
has_technology = "tech_xxx"     # Boolean check (trigger)
add_energy = 100                # Assignment (effect)
owner = { ... }                 # Scope change
```

### 4.2 Blocks & Braces

Code is organized in named blocks using `{ }`:

```
my_object = {
    property = value
    nested_block = {
        property = value
    }
}
```

### 4.3 Logic Operators

| Operator | Purpose | Example |
|----------|---------|---------|
| `AND = { }` | All conditions must be true | `AND = { condition1 condition2 }` |
| `OR = { }` | At least one must be true | `OR = { condition1 condition2 }` |
| `NOR = { }` | None must be true | `NOR = { condition1 condition2 }` |
| `NAND = { }` | At least one must be false | `NAND = { condition1 condition2 }` |
| `NOT = { }` | Invert single condition | `NOT = { has_trait = trait_x }` |

**Important:** `AND` is implicit when listing multiple conditions in a block. Explicit `AND` is usually unnecessary.

### 4.4 Comments

```
# This is a comment
```

### 4.5 Strings & Keys

- Most string values use quotes: `"tech_xxx"`, `"my_event.1"`
- Some don't require quotes: numbers, yes/no

### 4.6 Flow Control

```
# If/Else equivalent via limit
if = {
    limit = { condition }
    effect = { ... }
}

# Switch statement
switch = {
    trigger = has_trait
    trait_terraformer = { give_technology = tech_terrestrial_sculpting }
    trait_genalter = { give_technology = tech_gene_tailoring }
}

# While loop (effect)
while = {
    count = 5
    effect = { ... }
}

# Random list
random_list = {
    50 = { ... }
    30 = { ... }
    20 = { ... }
}
```

---

## 5. Scopes

**Scopes** are the context in which triggers and effects run. They represent game objects (country, planet, pop, fleet, etc.).

### 5.1 Scope Types (Object Types)

Common scope types include:

| Scope | Description |
|-------|-------------|
| `all` | Universal scope |
| `country` | Empire/faction |
| `planet` | Celestial body |
| `pop` | Population unit |
| `fleet` | Fleet |
| `ship` | Individual ship |
| `leader` | Leader character |
| `system` / `galactic_object` | Star system |
| `starbase` | Starbase |
| `sector` | Sector |
| `pop_faction` | Political faction |
| `army` | Ground army |
| `species` | Species |
| `war` | Active war |
| `federation` | Federation |
| `espionage_operation` | Spy operation |
| `first_contact` | First contact situation |

### 5.2 Scope Switching Keywords

Scope switches allow you to access related objects.

| Keyword | Switches To | Example Context |
|---------|-------------|-----------------|
| `this` | Current scope | Self-reference |
| `root` | The scope where the script started | Event's original target |
| `prev` | Previous scope in chain | Previous object in scope chain |
| `from` | Scope that called/triggered this | Event caller |
| `fromfrom` | FROM of FROM | Two events ago |
| `fromfromfrom` | FROM of FROMFROM | Three events ago |
| `owner` | Owner country | Planet -> Country |
| `controller` | Controller country | Occupied planet |
| `planet` | Planet the pop is on | Pop -> Planet |
| `solar_system` | System the object is in | Planet -> System |
| `star` | Star in the system | Planet -> Star |
| `capital_scope` | Empire capital | Country -> Capital Planet |
| `home_planet` | Species homeworld | Species -> Planet |
| `leader` | Leader of fleet/army | Fleet -> Leader |
| `species` | Species of pop/leader | Pop -> Species |
| `last_created_country` | Last created country | Global |
| `last_created_fleet` | Last created fleet | Global |
| `last_created_ship` | Last created ship | Global |
| `last_created_leader` | Last created leader | Global |
| `event_target:xxx` | Saved event target | Named saved scope |

**Example:**
```
country_event = {
    id = my_mod.1
    trigger = {
        # 'this' is the country
        any_owned_planet = {
            # now inside planet scope
            is_planet_class = pc_continental
            owner = {
                # switched back to country scope
                has_technology = tech_colonization_1
            }
        }
    }
}
```

### 5.3 Scope Iterators

Iterators loop over all objects of a type and check conditions.

| Iterator | Scope | Description |
|----------|-------|-------------|
| `any_owned_planet` | country | Any planet owned by country |
| `every_owned_planet` | country | Execute on every owned planet |
| `random_owned_planet` | country | Pick random owned planet |
| `any_planet` | system | Any planet in system |
| `any_pop` | planet | Any pop on planet |
| `any_ship` | fleet | Any ship in fleet |
| `any_country` | all | Any country in galaxy |
| `any_playable_country` | all | Any playable country |
| `any_neighbor_country` | country | Any neighboring country |
| `any_relation` | country | Any diplomatic relation |
| `any_system_ambient_object` | system | Any ambient object |

**Example:**
```
immediate = {
    every_owned_planet = {
        add_modifier = { modifier = my_mod_bonus days = 360 }
    }
}
```

---

## 6. Triggers (Conditions)

**Triggers** return `true` or `false`. They check the game state but do not modify it.

### 6.1 Common Trigger Patterns

```
# Simple value check
has_technology = "tech_xxx"
is_country_type = default
num_pops > 50
has_trait = trait_intelligent

# Scoped trigger
check_variable = {
    which = my_variable
    value >= 10
}

# Checking modifiers
check_modifier_value = {
    modifier = pop_growth_speed_reduction
    value > 1.05
}
```

### 6.2 Trigger Categories

Common triggers include:

**Country:**
- `has_technology = <tech>`
- `has_valid_civic = <civic>`
- `has_origin = <origin>`
- `has_ethic = <ethic>`
- `is_regular_empire = yes/no`
- `is_machine_empire = yes/no`
- `is_hive_empire = yes/no`
- `is_at_war = yes/no`
- `num_pops > <int>`
- `resource_stockpile_compare = { resource = energy value > 1000 }`

**Planet:**
- `is_planet_class = <class>`
- `has_modifier = <modifier>`
- `free_building_slots > <int>`
- `num_pops > <int>`
- `has_owner = yes/no`
- `is_colony = yes/no`
- `is_capital = yes/no`

**Pop:**
- `has_trait = <trait>`
- `is_same_species = <scope>`
- `is_robot_pop = yes/no`

**Fleet/Ship:**
- `fleet_power > <int>`
- `has_ship_flag = <flag>`

**General:**
- `exists = <scope>`
- `is_scope_type = <type>`
- `has_global_flag = <flag>`
- `has_country_flag = <flag>`
- `has_planet_flag = <flag>`

### 6.3 Scripted Triggers

Create reusable condition blocks in `common/scripted_triggers/`:

```
# common/scripted_triggers/my_triggers.txt
is_my_custom_empire = {
    is_regular_empire = yes
    OR = {
        has_valid_civic = civic_corporate_dominion
        has_valid_civic = civic_merchant_guilds
    }
}
```

Usage:
```
trigger = {
    is_my_custom_empire = yes
}
```

---

## 7. Effects

**Effects** modify game state. They are like functions that do things.

### 7.1 Common Effects by Category

**Resource & Modifiers:**
```
add_energy = 1000
add_minerals = 500
add_physics_research = 100
add_modifier = { modifier = my_modifier days = 360 }
remove_modifier = my_modifier
```

**Country/Empire:**
```
give_technology = { tech = tech_xxx message = no }
add_opinion_modifier = { modifier = my_opinion target = <scope> }
set_country_flag = my_flag
remove_country_flag = my_flag
change_government = { authority = auth_democratic }
add_research_option = tech_xxx
```

**Planet:**
```
add_building = building_capital
remove_building = building_colony_shelter
add_district = district_city
add_deposit = d_energy_1
terraform_planet = pc_gaia
change_pc = pc_continental
```

**Pop:**
```
kill_pop = yes
add_trait = trait_intelligent
remove_trait = trait_slow_learners
resettle_pop = { target = <planet> }
```

**Fleet/Ship:**
```
create_ship = { name = "My Ship" design = my_design }
add_experience = 1000
```

**Event & Scope:**
```
country_event = { id = my_event.1 days = 30 }
planet_event = { id = my_event.2 }
fire_on_action = { on_action = my_custom_action }
save_event_target_as = my_target
clear_global_event_target = my_target
```

**Variables:**
```
set_variable = { which = my_var value = 10 }
change_variable = { which = my_var value = 5 }
multiply_variable = { which = my_var value = 2 }
divide_variable = { which = my_var value = 2 }
```

### 7.2 Scripted Effects

Create reusable effect blocks in `common/scripted_effects/`:

```
# common/scripted_effects/my_effects.txt
grant_bonus_resources = {
    add_energy = 1000
    add_minerals = 500
    add_influence = 50
}
```

Usage:
```
immediate = {
    grant_bonus_resources = yes
}
```

**Parameterized Scripted Effects (Advanced):**
```
# Definition
add_scaled_resources = {
    add_energy = $ENERGY$
    add_minerals = $MINERALS$
}

# Usage
add_scaled_resources = yes |ENERGY|1000|MINERALS|500|
```

---

## 8. Modifiers

**Modifiers** influence gameplay state for a scope. They are passive bonuses or penalties.

### 8.1 Static Modifiers

Defined in `common/static_modifiers/`:

```
my_static_modifier = {
    planet_jobs_energy_produces_mult = 0.15
    pop_happiness = 0.05
    icon = "gfx/interface/icons/modifiers/mod_energy.dds"
}
```

### 8.2 Applying Modifiers

```
add_modifier = {
    modifier = my_static_modifier
    days = 3600
}
```

### 8.3 Triggered Modifiers

Only apply when conditions are met:

```
triggered_planet_modifier = {
    potential = {
        exists = owner
        owner = { is_regular_empire = yes }
    }
    planet_jobs_unity_produces_mult = 0.10
}
```

### 8.4 Common Modifier Types

Modifier names follow patterns:

| Pattern | Effect |
|---------|--------|
| `*_produces_mult` | Multiplies production |
| `*_produces_add` | Adds flat production |
| `*_upkeep_mult` | Multiplies upkeep |
| `*_cost_mult` | Multiplies cost |
| `*_speed_mult` | Multiplies speed |
| `pop_growth_speed` | Pop growth |
| `pop_happiness` | Happiness |
| `planet_stability_add` | Stability |
| `planet_crime_add` | Crime |

**Research Modifiers:**
- `all_technology_research_speed = 0.10`
- `category_biology_research_speed_mult = 0.15`

**Ship/Fleet Modifiers:**
- `ship_fire_rate_mult = 0.10`
- `ship_weapon_damage = 0.10`
- `ship_hull_mult = 0.20`

### 8.5 Scripted Modifiers

Introduced in patch 3.4. They allow custom dynamic modifiers:

```
# common/scripted_modifiers/my_scripted_modifiers.txt
my_scripted_modifier = {
    icon = "gfx/interface/icons/modifiers/mod_default.dds"
}
```

Note: Scripted modifiers do **nothing by default**. You must manually implement their effects by referencing `mult = modifier:my_scripted_modifier` in appropriate places.

---

## 9. Events

Events drive narrative and mechanics. They are defined in `events/`.

### 9.1 Event Types

| Type | Scope | Description |
|------|-------|-------------|
| `country_event` | country | Empire-wide event |
| `planet_event` | planet | Planet-specific event |
| `fleet_event` | fleet | Fleet event |
| `ship_event` | ship | Individual ship |
| `pop_event` | pop | Population unit |
| `pop_faction_event` | pop_faction | Political faction |
| `leader_event` | leader | Leader |
| `system_event` | galactic_object | Star system |
| `starbase_event` | starbase | Starbase |
| `observer_event` | observer | Debug/dev mode only |

### 9.2 Event Structure

```
country_event = {
    id = my_mod.1                          # Unique ID (namespace.number)
    title = my_mod.1.title                 # Localization key
    desc = my_mod.1.desc                   # Localization key
    picture = GFX_evt_throne_room          # Event picture
    show_sound = event_default             # Sound effect
    
    is_triggered_only = yes                # CRITICAL: prevents daily polling
    
    # Pre-triggers for quick filtering (performance)
    pre_triggers = {
        has_owner = yes
        is_ai = no
    }
    
    # Main trigger conditions
    trigger = {
        is_regular_empire = yes
        has_technology = tech_space_trading
    }
    
    # Mean Time To Happen (for non-triggered events)
    mean_time_to_happen = {
        months = 120
        modifier = {
            factor = 0.5
            has_ethic = ethic_materialist
        }
    }
    
    # Immediate effects (fire when event triggers)
    immediate = {
        set_country_flag = my_mod_flag
        add_energy = 500
    }
    
    # Player options
    option = {
        name = my_mod.1.option_a           # Button text
        trigger = {                        # Option availability
            has_resource = { type = energy amount > 1000 }
        }
        allow = { ... }                    # Additional checks
        
        custom_tooltip = my_mod.1.tooltip  # Tooltip text
        
        # Effects when chosen
        hidden_effect = {                  # Effects not shown in tooltip
            country_event = { id = my_mod.2 days = 30 }
        }
        add_modifier = { modifier = my_bonus days = 360 }
    }
    
    option = {
        name = my_mod.1.option_b
        trigger = { ... }
        # ...
    }
    
    # After-option effect
    after = {
        remove_country_flag = my_mod_flag
    }
}
```

### 9.3 Namespaces

Every event file must declare a namespace at the top:

```
namespace = my_mod
```

Event IDs then become `my_mod.1`, `my_mod.2`, etc.

### 9.4 Firing Events

```
# From effects
country_event = { id = my_mod.1 }
planet_event = { id = my_mod.2 days = 30 random = 15 }

# Fire with scope override
country_event = {
    id = my_mod.3
    scopes = { from = prev }
}

# On Actions (see below)
```

### 9.5 On Actions

Register events to fire on specific game events in `common/on_actions/`:

```
# common/on_actions/my_mod_on_actions.txt
on_game_start_country = {
    events = {
        my_mod.1
        my_mod.2
    }
}

on_monthly_pulse = {
    events = { my_mod.10 }
}

on_planet_surveyed = {
    events = { my_mod.anomaly.1 }
}
```

**Common On Actions:**

| On Action | Fires When |
|-----------|-----------|
| `on_game_start` | Game begins |
| `on_game_start_country` | Per country at start |
| `on_empire_initialized` | Empire created |
| `on_monthly_pulse` | Every month |
| `on_yearly_pulse` | Every year |
| `on_planet_surveyed` | Planet surveyed |
| `on_colonized` | Planet colonized |
| `on_war_beginning` | War declared |
| `on_war_ended` | War ends |
| `on_leader_death` | Leader dies |
| `on_building_complete` | Building finished |
| `on_tech_increased` | Tech researched |
| `empire_init_add_technologies` | Grant starting techs |
| `empire_init_capital_planet` | Setup homeworld |

**Custom On Actions:**
```
# Fire custom on_action
fire_on_action = {
    on_action = my_custom_action
    scopes = { from = prev }
}
```

### 9.6 Event Best Practices

- **Always use `is_triggered_only = yes`** unless you specifically need daily/monthly polling
- Use `pre_triggers` for performance when possible
- Use `hide_window = yes` for background events
- Use `hidden_effect = { }` to hide effects from tooltips

---

## 10. Variables & Dynamic Scripting

### 10.1 Basic Variables

```
# Set variable
set_variable = { which = my_var value = 10 }

# Math operations
change_variable = { which = my_var value = 5 }      # Add
change_variable = { which = my_var value = -3 }     # Subtract
multiply_variable = { which = my_var value = 2 }    # Multiply
divide_variable = { which = my_var value = 2 }      # Divide

# Cross-scope variables
set_variable = { which = my_var value = event_target:other_country.my_var }
set_variable = { which = my_var value = prev.my_var }
```

### 10.2 Variable Triggers

```
check_variable = {
    which = my_var
    value >= 10
}

check_variable_arithmetic = {
    which = my_var
    multiply = 2
    add = 5
    value > 20
}
```

### 10.3 Exporting Values to Variables

```
export_modifier_to_variable = {
    modifier = pop_growth_speed_reduction
    variable = my_var
}

export_resource_stockpile_to_variable = {
    resource = energy
    variable = my_energy
}

export_resource_income_to_variable = {
    resource = minerals
    variable = my_mineral_income
}

export_trigger_value_to_variable = {
    trigger = num_pops
    variable = my_pop_count
}
```

### 10.4 Using Variables in Effects

```
add_resource = {
    type = energy
    amount = my_var              # Use variable as amount
}

add_modifier = {
    modifier = my_modifier
    mult = my_var                # Multiply modifier effect
}
```

### 10.5 Scripted Variables

Global constants defined in `common/scripted_variables/`:

```
# common/scripted_variables/my_vars.txt
@my_constant = 100
@tech_cost_t1 = 2000
```

Usage:
```
cost = @tech_cost_t1
```

**Important:** Scripted variable files must end with a blank line or comment.

### 10.6 Script Values

Dynamic numeric calculations (introduced in patch 3.3):

```
# common/script_values/my_values.txt
my_calculated_value = {
    base = 10
    modifier = {
        add = 5
        has_trait = trait_intelligent
    }
    modifier = {
        multiply = 2
        is_regular_empire = yes
    }
}
```

Usage:
```
mult = value:my_calculated_value
```

**Parameterized Script Values:**
```
# Definition
my_scaled_value = {
    base = $BASE$
    modifier = { multiply = $MULT$ }
}

# Usage
value:my_scaled_value|BASE|10|MULT|2|
```

### 10.7 Inline Scripts

Reusable script snippets introduced in patch 3.5:

```
# common/inline_scripts/my_inline.txt
produces = {
    energy = $AMOUNT$
}

# Usage in another file
inline_script = {
    script = my_inline
    AMOUNT = 5
}
```

---

## 11. Common Game Object Definitions

### 11.1 Buildings

`common/buildings/`:

```
building_example = {
    base_buildtime = 360
    category = resource
    
    potential = {
        exists = owner
        owner = { is_regular_empire = yes }
    }
    
    allow = {
        has_upgraded_capital = yes
    }
    
    destroy_if = {
        exists = owner
        owner = { is_regular_empire = no }
    }
    
    planet_modifier = {
        job_researcher_add = 2
    }
    
    triggered_planet_modifier = {
        potential = { exists = owner }
        planet_research_produces_mult = 0.10
    }
    
    resources = {
        category = planet_buildings
        cost = { minerals = 500 }
        upkeep = { energy = 2 }
        produces = {
            trigger = { exists = owner }
            physics_research = 5
            mult = value:my_script_value
        }
    }
    
    ai_weight = {
        weight = 100
        modifier = {
            factor = 0
            owner = { has_country_flag = disable_ai_building }
        }
    }
    
    on_built = {
        planet_event = { id = my_mod.building_built }
    }
    
    on_destroyed = {
        # effects when destroyed
    }
}
```

### 11.2 Technologies

`common/technology/`:

```
tech_example = {
    cost = @tier1cost1
    area = society
    tier = 1
    category = { biology }
    
    prerequisites = { "tech_gene_tailoring" }
    
    weight = @tier1weight1
    
    potential = {
        is_regular_empire = yes
    }
    
    modifier = {
        pop_growth_speed = 0.10
    }
    
    weight_modifier = {
        modifier = {
            factor = 1.5
            research_leader = {
                area = society
                has_trait = leader_trait_expertise_biology
            }
        }
    }
    
    ai_weight = {
        modifier = {
            factor = 1.5
            has_ethic = ethic_pacifist
        }
    }
    
    is_rare = yes
    is_dangerous = no
}
```

### 11.3 Armies

`common/armies/`:

```
my_army = {
    damage = 1.50
    health = 1.20
    morale = 1.00
    morale_damage = 1.00
    collateral_damage = 1.00
    war_exhaustion = 1.00
    time = 90
    
    icon_frame = 2
    
    has_species = yes
    
    prerequisites = { "tech_assault_armies" }
    
    resources = {
        category = armies
        cost = { minerals = 150 }
        upkeep = { energy = 1 }
    }
    
    potential = {
        from = { is_sapient = yes }
        owner = { is_regular_empire = yes }
    }
    
    allow = {
        custom_tooltip = {
            text = my_army_requirement
            owner = { has_technology = tech_xxx }
        }
    }
}
```

### 11.4 Civics

`common/governments/civics/`:

```
civic_my_civic = {
    modification = no
    
    playable = {
        host_has_dlc = "Utopia"
    }
    
    potential = {
        ethics = { NOT = { value = ethic_gestalt_consciousness } }
        authority = { NOT = { value = auth_corporate } }
    }
    
    possible = {
        civics = {
            NOR = {
                value = civic_fanatic_purifiers
            }
        }
    }
    
    random_weight = {
        base = 5
        modifier = {
            factor = 0
            NOT = { host_has_dlc = "Utopia" }
        }
    }
    
    modifier = {
        planet_jobs_energy_produces_mult = 0.15
    }
    
    description = civic_tooltip_my_civic_effects
    
    # For origins with secondary species
    has_secondary_species = {
        title = civic_my_civic_secondary_species
        traits = { trait = trait_cybernetic }
    }
}
```

### 11.5 Edicts

`common/edicts/`:

```
edict_my_edict = {
    length = 3600
    
    resources = {
        category = edicts
        cost = { influence = 100 }
    }
    
    potential = {
        is_regular_empire = yes
    }
    
    allow = {
        NOT = { has_edict = edict_my_other_edict }
    }
    
    modifier = {
        planet_jobs_unity_produces_mult = 0.15
    }
    
    ai_weight = {
        weight = 100
    }
}
```

### 11.6 Policies

`common/policies/`:

```
my_policy = {
    potential = {
        is_country_type = default
    }
    
    option = {
        name = "my_policy_option_1"
        
        potential = { is_regular_empire = yes }
        valid = { is_at_war = no }
        
        policy_flags = { my_policy_flag }
        
        modifier = {
            country_resource_unity_mult = 0.10
        }
        
        AI_weight = {
            weight = 10
        }
    }
    
    option = {
        name = "my_policy_option_2"
        # ...
    }
}
```

### 11.7 Traits

`common/traits/`:

```
trait_my_trait = {
    cost = 2
    potential_crossbreeding_chance = 1.0
    
    allowed_archetypes = { BIOLOGICAL LITHOID }
    
    opposites = { "trait_slow_learners" }
    
    modifier = {
        planet_jobs_physics_research_produces_mult = 0.10
        planet_jobs_engineering_research_produces_mult = 0.10
        planet_jobs_society_research_produces_mult = 0.10
    }
    
    slave_cost = {
        energy = 100
    }
    
    icon = "gfx/interface/icons/traits/trait_my_trait.dds"
}
```

### 11.8 Strategic Resources

`common/strategic_resources/`:

```
my_resource = {
    tradable = yes
    max = 15000
    
    fixed_max_amount = no
    
    ai_weight = {
        weight = 100
    }
    
    ai_wants = {
        base = 100
    }
    
    deficit_situation = situation_resource_deficit
    
    # Market stuff
    market_amount = 100
    market_price = 100
}
```

---

## 12. Localization

### 12.1 File Format

- Files: `localisation/<language>/mymod_l_english.yml`
- **Encoding: UTF-8 with BOM**
- First line must declare language

```yml
l_english:
 my_mod.1.title:0 "The Discovery"
 my_mod.1.desc:0 "We have discovered something remarkable in the void."
 my_mod.1.option_a:0 "Fascinating."
 my_mod.1.option_a.tooltip:0 "Gain §G100§! Physics Research."
 
 mod_planet_jobs_energy_produces_mult:0 "Energy Production"
 mod_planet_jobs_energy_produces_mult_desc:0 "Increases energy production from jobs."
```

### 12.2 Localization Keys

- `:0` after the key is the version number (usually 0)
- Use `§Ggreen§!`, `§Rred§!`, `§Yyellow§!`, `§Bblue§!` for colored text
- `\n` for line breaks

### 12.3 Scoped Localization (Bracket Commands)

```yml
my_event.desc:0 "The [root.GetName] system holds many secrets. [from.GetSpeciesNamePlural] are watching."
```

Common scoped commands:
- `[this.GetName]` / `[root.GetName]`
- `[owner.GetName]`
- `[from.GetSpeciesName]` / `[from.GetSpeciesNamePlural]`
- `[planet.GetClassName]`
- `[leader.GetName]`
- `[this.GetAdj]` (adjective)
- `[this.GetRulerTitle]`

### 12.4 Custom Text Icons

Define in a `.gfx` file:
```
spriteTypes = {
    spriteType = {
        name = "GFX_text_my_icon"
        texturefile = "gfx/interface/icons/text_icons/my_icon.dds"
    }
}
```

Use in localization:
```yml
my_string:0 "£my_icon£ This has a custom icon."
```

---

## 13. Graphics & Interface

### 13.1 Icon Definitions

Most game objects automatically look for icons based on their internal key:

```
# A building named "building_example" automatically looks for:
gfx/interface/icons/buildings/building_example.dds

# A tech named "tech_example" automatically looks for:
gfx/interface/icons/technologies/tech_example.dds
```

### 13.2 Manual Sprite Definitions (.gfx)

```
spriteTypes = {
    spriteType = {
        name = "GFX_event_picture_my_event"
        texturefile = "gfx/event_pictures/my_event_picture.dds"
        masking_texture = "gfx/interface/situation_log/event_mask.dds"
        alwaystransparent = yes
    }
    
    spriteType = {
        name = "GFX_tradition_example"
        textureFile = "gfx/interface/icons/traditions/tr_example.dds"
    }
}
```

### 13.3 UI Layout (.gui)

```
guiTypes = {
    containerWindowType = {
        name = "my_custom_window"
        position = { x = 100 y = 100 }
        size = { width = 400 height = 300 }
        
        buttonType = {
            name = "my_button"
            position = { x = 10 y = 10 }
            quadTextureSprite = "GFX_button_animated_95_34"
            buttonText = "my_button_label"
        }
        
        instantTextBoxType = {
            name = "my_text"
            position = { x = 10 y = 50 }
            font = "cg_16b"
            text = "Hello World"
            maxWidth = 300
            maxHeight = 200
        }
    }
}
```

### 13.4 Icon Resolutions

| Asset Type | Resolution |
|-----------|-----------|
| Civic icons | 28x28 |
| Building icons | 38x38 |
| Trait icons | 28x28 |
| Tech icons | 48x48 |
| Tradition icons | 48x48 |
| Event pictures | 461x281 |
| Flag symbols | 128x128 |
| Standard text icons | 16x16 |

---

## 14. Debugging & Console Commands

### 14.1 Opening the Console

Press one of: `~`, `` ` ``, `^`, `°`, `²`, `§`, `Shift+2`, `Shift+3`, `ALT+2+1` (varies by keyboard layout).

### 14.2 Essential Debug Commands

| Command | Purpose |
|---------|---------|
| `debugtooltip` | Shows IDs when hovering over objects |
| `trigger_docs` | Generates `triggers.log`, `effects.log`, `scopes.log` in user documents |
| `reloadevents` | Reloads all event files (may take time) |
| `reload` | Reloads specific file type (rarely works for everything) |
| `reloadfx` | Reloads shader effects |
| `reloadgui` | Reloads GUI files |
| `error` | Opens error.log location |
| `game` | Opens game.log |
| `observe` | Enter observer mode |

### 14.3 Testing Script in Console

```
# Test an effect on selected scope
effect add_energy = 1000
effect add_modifier = { modifier = my_modifier days = 360 }

# Test a trigger
trigger has_technology = tech_xxx

# Fire an event
event my_mod.1
event my_mod.1 0    # 0 = target ID (empire 0)

# Get IDs with debugtooltip first
```

### 14.4 Log Files

| Log | Location |
|-----|----------|
| `error.log` | `logs/error.log` |
| `game.log` | `logs/game.log` |
| `script_documentation/effects.log` | Auto-generated trigger docs |
| `script_documentation/triggers.log` | Auto-generated trigger docs |
| `script_documentation/scopes.log` | Auto-generated scope docs |
| `script_documentation/localizations.log` | Auto-generated loc docs |

**Note:** By default, identical log messages are only printed once. Vary the message string if logging in loops.

### 14.5 Effect Console Commands

```
# Grant all leader traits (example)
effect while = { count = 50 country_event = { id = story.335 } }

# Add modifiers to root
effect root = { add_modifier = { modifier = carbon_world } }
```

---

## 15. Best Practices & Tools

### 15.1 Modding Best Practices

1. **Never overwrite vanilla files** unless absolutely necessary (e.g., `on_actions`).
2. Use **events** instead of overwriting game objects when possible — more compatible.
3. **Use scripted triggers/effects** to keep code DRY (Don't Repeat Yourself).
4. Always use `is_triggered_only = yes` for events.
5. Use `pre_triggers` for performance on ambient events.
6. **Name files uniquely** to avoid conflicts (e.g., `my_mod_buildings.txt`).
7. Use **CWTools** for validation and auto-complete.
8. Check `error.log` frequently.
9. Clear GFX cache when changing textures.
10. Test console commands to iterate quickly.

### 15.2 Load Order

Stellaris loads mods primarily by **file naming**, not by launcher load order (with some exceptions for UI and same-filename overrides).

- **Submods** must load after the main mod (they use same filenames).
- **UI mods** often need to load last because they overwrite interface files.
- **Patch mods** should load after the mods they patch.

### 15.3 Tools

| Tool | Purpose |
|------|---------|
| **VS Code + CWTools** | Syntax highlighting, validation, auto-complete |
| **Notepad++** | Quick search across vanilla files |
| **GIMP / Paint.NET** | Edit `.dds` textures |
| **Blender + JoroDox** | 3D model creation |

### 15.4 Compatibility & Merging

- Use **Irony Mod Manager** for conflict detection and patch generation.
- Use `dependency = "Other Mod Name"` in descriptor to ensure load order.
- When two mods modify the same file, **the last loaded file wins**.

---

## 16. Key Resources

| Resource | Link |
|----------|------|
| **Stellaris Wiki (Paradox)** | https://stellaris.paradoxwikis.com/Modding |
| **Stellaris Fandom Wiki** | https://stellaris.fandom.com/wiki/Modding |
| **Script Reference Wiki** | https://stellaris-script-reference.fandom.com/ |
| **OldEnt Triggers/Effects List** | https://github.com/OldEnt/stellaris-triggers-modifiers-effects-list |
| **Stellaris Modding Den Discord** | Primary community hub |
| **CWTools (VS Code Extension)** | Search "CWTools" in VS Code marketplace |
| **Console Commands List** | https://stellariscommands.com/ |

### 16.1 Auto-Generated Documentation

Run the `trigger_docs` console command in-game to generate these files in your user documents:
- `logs/script_documentation/triggers.log`
- `logs/script_documentation/effects.log`
- `logs/script_documentation/scopes.log`
- `logs/script_documentation/localizations.log`
- `logs/script_documentation/modifiers.log`

These contain the **most up-to-date** lists for your specific game version.

---

## Appendix: Quick Reference Card

### Event Template
```
namespace = my_mod

country_event = {
    id = my_mod.1
    title = my_mod.1.title
    desc = my_mod.1.desc
    picture = GFX_evt_default
    show_sound = event_default
    is_triggered_only = yes
    
    trigger = {
        is_regular_empire = yes
    }
    
    immediate = {
        set_country_flag = my_mod_flag
    }
    
    option = {
        name = my_mod.1.option_a
        add_energy = 500
    }
    
    option = {
        name = my_mod.1.option_b
        add_minerals = 500
    }
    
    after = {
        remove_country_flag = my_mod_flag
    }
}
```

### Scripted Effect Template
```
my_effect = {
    add_energy = 1000
    add_minerals = 500
    set_country_flag = my_flag
}
```

### Scripted Trigger Template
```
my_trigger = {
    is_regular_empire = yes
    has_technology = tech_space_trading
}
```

### Building Template
```
building_example = {
    base_buildtime = 360
    category = resource
    potential = { exists = owner }
    allow = { has_upgraded_capital = yes }
    planet_modifier = { job_researcher_add = 2 }
    resources = {
        category = planet_buildings
        cost = { minerals = 500 }
        upkeep = { energy = 2 }
        produces = { physics_research = 5 }
    }
}
```

---

*End of Guide. Happy Modding!*
