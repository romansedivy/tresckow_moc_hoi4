# Hearts of Iron 4 Modding Guide

## Table of Contents
1. [Introduction](#introduction)
2. [Basic Syntax and Structure](#basic-syntax-and-structure)
3. [File Structure Overview](#file-structure-overview)
4. [Essential Modding Components](#essential-modding-components)
5. [Common Syntax Elements](#common-syntax-elements)
6. [Examples and Code Snippets](#examples-and-code-snippets)
7. [Advanced Modding Topics](#advanced-modding-topics)
8. [Debugging and Testing](#debugging-and-testing)

## Introduction

Hearts of Iron 4 (HOI4) uses a custom scripting language for modding. This guide will help you understand the syntax, file structure, and key concepts needed to create effective mods.

**Key Points:**
- HOI4 modding doesn't require programming knowledge, but you need to understand **SYNTAX**
- Files use UTF-8 encoding
- No spaces allowed in identifiers - use underscores instead
- Case sensitive
- Comments start with `#`

## Basic Syntax and Structure

### Basic Syntax Rules

```
# This is a comment
variable_name = value
another_variable = "string value"

# Blocks use curly braces
block_name = {
    nested_variable = 10
    another_nested = yes
}

# Arrays/Lists
list_name = { item1 item2 item3 }
```

### Data Types

- **Strings**: `"text"` or `text` (quotes optional for simple strings)
- **Numbers**: `10`, `1.5`, `-5`
- **Booleans**: `yes`/`no`, `true`/`false`
- **Dates**: `1936.1.1` (YYYY.MM.DD format)

## File Structure Overview

Your mod should follow this structure:

```
mod_name/
├── descriptor.mod          # Mod information
├── common/                 # Game mechanics and data
│   ├── countries/          # Country definitions
│   ├── national_focus/     # Focus trees
│   ├── technologies/       # Technology definitions
│   ├── units/              # Unit types
│   ├── characters/         # Leaders and advisors
│   ├── ideas/              # National spirits and ideas
│   ├── decisions/          # Decision categories
│   └── ...
├── events/                 # Event files
├── history/                # Historical setup
│   ├── countries/          # Country starting conditions
│   ├── states/             # State ownership and resources
│   └── units/              # Starting military units
├── localisation/           # Text translations
│   ├── english/
│   ├── german/
│   └── ...
├── gfx/                    # Graphics and images
│   ├── flags/              # Country flags
│   ├── leaders/            # Leader portraits
│   └── interface/          # UI elements
└── interface/              # UI definitions
```

## Essential Modding Components

### 1. Descriptor File (descriptor.mod)

```
version="1.0"
tags={
    "Military"
    "Historical"
}
name="My Awesome Mod"
path="mod/my_mod"
supported_version="1.15.3"
```

### 2. Events

Events are triggered actions that present choices to players.

```
add_namespace = my_mod

# Country Event
country_event = {
    id = my_mod.1
    title = my_mod.1.t
    desc = my_mod.1.d
    picture = GFX_report_event_generic
    
    fire_only_once = yes
    is_triggered_only = yes
    
    trigger = {
        tag = GER
        date > 1936.6.1
    }
    
    option = {
        name = my_mod.1.a
        add_political_power = 50
        ai_chance = { factor = 80 }
    }
    
    option = {
        name = my_mod.1.b
        add_stability = 0.1
        ai_chance = { factor = 20 }
    }
}
```

### 3. National Focus Trees

Focus trees define a country's political and military development paths.

```
focus_tree = {
    id = my_country_focus
    
    country = {
        factor = 0
        modifier = {
            add = 10
            tag = MCO  # My Country tag
        }
    }
    
    focus = {
        id = MCO_industrial_expansion
        icon = GFX_goal_generic_construct_civilian
        cost = 10
        
        x = 5
        y = 0
        
        completion_reward = {
            add_tech_bonus = {
                name = industrial_bonus
                bonus = 0.5
                uses = 2
                category = industry
            }
        }
    }
    
    focus = {
        id = MCO_military_buildup
        icon = GFX_goal_generic_allies_build_infantry
        cost = 10
        
        prerequisite = { focus = MCO_industrial_expansion }
        
        x = 5
        y = 1
        
        completion_reward = {
            army_experience = 25
            add_tech_bonus = {
                name = land_doctrine_bonus
                bonus = 0.5
                uses = 1
                category = land_doctrine
            }
        }
    }
}
```

### 4. Localisation

Localisation files provide text for different languages.

```yaml
# localisation/english/my_mod_l_english.yml
l_english:
 my_mod.1.t:0 "Important Decision"
 my_mod.1.d:0 "Our nation faces a crucial choice that will shape our future."
 my_mod.1.a:0 "Increase Political Power"
 my_mod.1.b:0 "Improve Stability"
 
 MCO_industrial_expansion:0 "Industrial Expansion"
 MCO_industrial_expansion_desc:0 "We must modernize our industry to compete with other nations."
 MCO_military_buildup:0 "Military Buildup"
 MCO_military_buildup_desc:0 "Our armed forces need strengthening to defend our interests."
```

### 5. Countries

Country definitions specify graphical culture and basic properties.

```
# common/countries/my_country.txt
graphical_culture = western_european_gfx
graphical_culture_2d = western_european_2d

color = { 150 75 200 }
```

Country tags link countries to their definitions:

```
# common/country_tags/00_countries.txt
MCO = "countries/my_country.txt"
```

### 6. Technologies

Custom technologies can be added to research trees.

```
# common/technologies/my_technologies.txt
technologies = {
    advanced_radar = {
        enable_equipments = {
            radar_equipment_2
        }
        
        research_cost = 2
        start_year = 1940
        folder = {
            name = electronics_folder
            position = { x = 2 y = 6 }
        }
        
        ai_will_do = {
            factor = 1
        }
        
        categories = {
            electronics
            radar_tech
        }
    }
}
```

## Common Syntax Elements

### Triggers and Conditions

Triggers determine when something can happen:

```
trigger = {
    tag = GER
    has_war = yes
    date > 1939.1.1
    NOT = { has_idea = war_economy }
    OR = {
        has_tech = radar_tech
        has_tech = sonar_tech
    }
}
```

### Effects

Effects are actions that happen when triggered:

```
effect = {
    add_political_power = 100
    add_stability = 0.1
    add_war_support = 0.05
    set_country_flag = my_flag
    country_event = { id = my_mod.2 days = 30 }
}
```

### Scopes

Scopes target specific objects:

```
# Target all countries
every_country = {
    limit = { is_major = yes }
    add_political_power = 50
}

# Target random state
random_owned_state = {
    limit = { is_core_of = ROOT }
    add_building_construction = {
        type = industrial_complex
        level = 2
        instant_build = yes
    }
}
```

### Modifiers

Modifiers apply bonuses or penalties:

```
modifier = {
    army_morale_factor = 0.1
    industrial_capacity_factory = 0.15
    production_speed_buildings_factor = 0.2
    local_manpower = 0.25
}
```

## Examples and Code Snippets

### Creating a Custom Decision

```
# common/decisions/my_decisions.txt
my_decision_category = {
    mobilize_reserves = {
        icon = generic_prepare_civil_war
        
        available = {
            has_war = yes
            manpower > 100000
        }
        
        visible = {
            has_war = yes
        }
        
        cost = 100
        days_remove = 30
        
        modifier = {
            conscription_factor = 0.02
        }
        
        complete_effect = {
            add_manpower = 50000
            add_war_support = 0.05
        }
        
        ai_will_do = {
            factor = 1
            modifier = {
                factor = 2
                has_manpower < 500000
            }
        }
    }
}
```

### Custom Unit Type

```
# common/units/my_units.txt
sub_units = {
    elite_infantry = {
        sprite = infantry
        map_icon_category = infantry
        
        priority = 600
        ai_priority = 200
        active = no
        
        type = {
            infantry
            elite
        }
        
        group = infantry
        
        categories = {
            category_front_line
            category_light_infantry
            category_all_infantry
            category_army
        }
        
        combat_width = 2
        
        need = {
            infantry_equipment = 100
            support_equipment = 10
        }
        
        manpower = 1000
        training_time = 120
        
        max_strength = 25
        max_organisation = 70
        default_morale = 0.3
        
        # Combat stats
        soft_attack = 6
        hard_attack = 4
        air_attack = 0
        defense = 10
        breakthrough = 2
        armor = 0
        piercing = 5
    }
}
```

### Custom Idea/National Spirit

```
# common/ideas/my_ideas.txt
ideas = {
    country = {
        industrial_revolution = {
            picture = generic_production_bonus
            
            modifier = {
                production_speed_buildings_factor = 0.15
                industrial_capacity_factory = 0.1
            }
        }
        
        military_tradition = {
            picture = generic_army_war_college
            
            modifier = {
                army_morale_factor = 0.1
                planning_speed = 0.25
            }
        }
    }
}
```

## Advanced Modding Topics

### Scripted Effects

Create reusable effects:

```
# common/scripted_effects/my_effects.txt
boost_industry = {
    add_tech_bonus = {
        name = industrial_bonus
        bonus = 0.5
        uses = 2
        category = industry
    }
    
    random_owned_state = {
        limit = { 
            free_building_slots = {
                building = industrial_complex
                size > 0
            }
        }
        add_building_construction = {
            type = industrial_complex
            level = 2
            instant_build = yes
        }
    }
}
```

### Scripted Triggers

Create reusable conditions:

```
# common/scripted_triggers/my_triggers.txt
is_ready_for_war = {
    has_war_support > 0.5
    has_political_power > 200
    num_of_military_factories > 20
    manpower > 500000
}
```

### Custom AI Behavior

```
# common/ai_strategy/my_ai.txt
GER_historical_strategy = {
    enable = {
        tag = GER
        has_game_rule = {
            rule = GER_ai_behavior
            option = DEFAULT
        }
    }
    
    ai_strategy = {
        type = role_ratio
        id = garrison
        value = 5
    }
    
    ai_strategy = {
        type = equipment_production_factor
        id = fighter
        value = 20
    }
}
```

## Debugging and Testing

### Debug Mode Setup

1. Create a shortcut to `hoi4.exe`
2. Add `-debug` to the target path
3. Launch with this shortcut to enable debug mode

### Common Debug Commands

```
# In console (press ~ to open)
add_political_power 1000
add_stability 0.5
add_war_support 0.5
research_on_icon_click
instant_construct
ai
debug_mode
```

### Error Checking

- Check `error.log` in your Documents/Paradox Interactive/Hearts of Iron IV/logs/
- Use the in-game console to test effects
- Validate syntax with text editors that support bracket matching

### Best Practices

1. **Start Small**: Begin with simple modifications before attempting complex features
2. **Copy Existing Files**: Use vanilla files as templates
3. **Test Frequently**: Test changes incrementally
4. **Use Comments**: Document your code for future reference
5. **Backup**: Keep backups of working versions
6. **Validate Syntax**: Check for proper bracket matching and syntax

### Performance Tips

- Avoid complex triggers that run frequently
- Use `limit` clauses to reduce scope iterations
- Cache frequently used scripted triggers
- Optimize AI strategies for better performance

## Conclusion

This guide covers the fundamental aspects of HOI4 modding. The key to successful modding is understanding the syntax, starting with simple modifications, and gradually building complexity. Always test your changes and refer to vanilla files for examples.

For more advanced topics, consult the official Paradox modding documentation and community resources like the HOI4 Modding Discord and Reddit communities.

Remember: Modding is an iterative process. Don't be afraid to experiment and learn from errors!