# Hearts of Iron 4 Modding Guide: Industry Technology Extension

## Overview

This guide explains how to create mods for Hearts of Iron 4, specifically focusing on extending the industry technology tree from 1945 to 1990. This mod extends the vanilla industry technologies with new technologies appearing every 3 years, maintaining the same progression patterns while bridging the gap that existed in the original cold war extension.

## What We've Accomplished

### 1. Extended Industry Technology Tree (1948-1990)

The mod successfully extends four main branches of the industry technology tree:

- **Concentrated Industry**: 15 new technologies (concentrated_industry6 to concentrated_industry20)
- **Dispersed Industry**: 15 new technologies (dispersed_industry6 to dispersed_industry20)
- **Construction**: 15 new technologies (construction6 to construction20)
- **Excavation**: 15 new technologies (excavation6 to excavation20)

### 2. Timeline Structure

Each technology follows a 3-year progression:
- 1948, 1951, 1954, 1957, 1960, 1963, 1966, 1969, 1972, 1975, 1978, 1981, 1984, 1987, 1990

### 3. Technology Positioning

Technologies are positioned using Y-coordinates that correspond to years:
```
@1948 = 12
@1951 = 14
@1954 = 16
[... and so on ...]
@1990 = 40
```

### 4. Progression Mechanics

- **Research Costs**: Gradually increase from 2.5 (1948) to 10 (1990)
- **AI Behavior**: Configured with date-based modifiers for realistic AI research patterns
- **Technology Paths**: Each technology properly connects to the next in sequence
- **Effects**: Maintain consistent bonuses following vanilla patterns

## Key Files Modified

### 1. Technology Definitions (`common/technologies/industry.txt`)

The main technology file was extended with:
- Connection paths from vanilla tech tree (excavation5 connects to all new branches)
- Year position constants for GUI positioning
- 60 new technology definitions with proper effects, AI behavior, and connections

### 2. GUI Updates (`countrytechtreeview.gui`)

Updated the tech tree GUI to display years properly:
- Added year labels from 1948 to 1990
- Corrected positioning for both left and right year displays
- Fixed the gap between 1945 and 1950 that existed previously

### 3. English Localization (`localisation/english/cold_war_l_english.yml`)

Added comprehensive English names and descriptions for all 60 new technologies, following historical technological progression.

### 4. German Localization (`localisation/german/cold_war_l_german.yml`)

Created authentic German translations using proper industrial terminology, maintaining the immersive feel for German players.

## Hearts of Iron 4 Modding Fundamentals

### Understanding Technology Files

HOI4 technology files use a specific structure:

```
technologies = {
    technology_name = {
        # Effects
        industrial_capacity_factory = 0.15
        
        # Research paths
        path = {
            leads_to_tech = next_technology
            research_cost_coeff = 1
        }
        
        # Basic properties
        research_cost = 2.5
        start_year = 1948
        
        # GUI positioning
        folder = {
            name = industry_folder
            position = { x = 4 y = 12 }
        }
        
        # AI behavior
        ai_will_do = {
            factor = 4
            modifier = {
                factor = 15
                date > "1948.1.1"
            }
        }
        
        # Categories for bonuses
        categories = {
            industry
            concentrated_industry_category
        }
    }
}
```

### Key Modding Principles

1. **Consistency**: Maintain consistent naming conventions and effect magnitudes
2. **Balance**: Ensure new technologies don't break game balance
3. **AI Compatibility**: Configure AI behavior to use new technologies appropriately
4. **Localization**: Always provide proper names and descriptions in all languages
5. **GUI Integration**: Update interface files to display new content properly

### Technology Positioning System

The Y-coordinate system in HOI4 tech trees follows this pattern:
- Each Y-position represents roughly 2 years of game time
- Vanilla technologies end around Y=10 (1943-1945)
- Our extension starts at Y=12 (1948) and continues to Y=40 (1990)

### Effect Types for Industry Technologies

Common effects used in industry technologies:
- `industrial_capacity_factory`: Increases factory output
- `industrial_capacity_dockyard`: Increases dockyard output
- `production_efficiency_cap_factor`: Raises production efficiency ceiling
- `production_efficiency_gain_factor`: Speeds up efficiency gain
- `global_building_slots_factor`: Increases building slots
- `production_speed_buildings_factor`: Faster construction
- `industry_repair_factor`: Faster damage repair
- `local_resources_factor`: Better resource extraction

## Creating Your Own Technology Extensions

### Step 1: Plan Your Technology Tree

1. Determine the time period you want to cover
2. Decide on the number of technologies and their spacing
3. Choose appropriate effects and their magnitude
4. Plan the visual layout in the tech tree

### Step 2: Create Technology Definitions

1. Add your technologies to the appropriate file in `common/technologies/`
2. Ensure proper connection paths between technologies
3. Set appropriate research costs, start years, and positions
4. Configure AI behavior with realistic priorities

### Step 3: Update the GUI

1. Modify `interface/countrytechtreeview.gui` to add year labels
2. Adjust positioning if necessary
3. Ensure the tech tree displays correctly in-game

### Step 4: Create Localizations

1. Add entries to all language files in `localisation/`
2. Use authentic terminology for the time period
3. Provide both names and descriptions for each technology

### Step 5: Test and Balance

1. Load the mod in-game and verify all technologies appear
2. Test AI behavior to ensure technologies are researched appropriately
3. Check balance to ensure effects aren't overpowered
4. Verify GUI displays correctly at different resolutions

## Advanced Modding Techniques

### Dynamic Effect Scaling

For longer technology trees, consider scaling effects based on the year:

```
# Early technologies (1948-1960)
industrial_capacity_factory = 0.10

# Mid-period technologies (1960-1975)
industrial_capacity_factory = 0.12

# Late technologies (1975-1990)
industrial_capacity_factory = 0.15
```

### Complex AI Behavior

Create sophisticated AI decision-making:

```
ai_will_do = {
    factor = 4
    
    # Boost priority for major powers
    modifier = {
        factor = 2
        is_major = yes
    }
    
    # Reduce priority if at war (focus on military tech)
    modifier = {
        factor = 0.5
        has_war = yes
    }
    
    # Historical flavor for specific countries
    modifier = {
        factor = 3
        tag = USA
        date > "1950.1.1"
    }
}
```

### Integration with Other Systems

Connect your technologies to other game systems:

```
# Unlock new buildings
enable_building = {
    building = advanced_factory
    level = 1
}

# Trigger events
on_research_complete = {
    country_event = {
        id = industry_revolution.1
    }
}

# Modify equipment stats
modify_unit_stat = {
    infantry_equipment = {
        production_cost = -0.1
    }
}
```

## Best Practices

1. **Version Control**: Use Git to track changes to your mod files
2. **Backup**: Always keep backups of original game files before modifying
3. **Testing**: Test your mod thoroughly in different scenarios
4. **Documentation**: Document your changes for future reference
5. **Community**: Engage with the modding community for feedback and assistance

## Troubleshooting Common Issues

### Technologies Not Appearing
- Check file syntax for errors
- Verify folder structure is correct
- Ensure technology names are unique

### GUI Display Problems
- Check positioning coordinates
- Verify year labels match technology years
- Test at different screen resolutions

### AI Not Researching Technologies
- Review AI behavior factors
- Check date conditions
- Verify prerequisites are met

### Localization Not Working
- Check file encoding (UTF-8 with BOM)
- Verify key names match exactly
- Ensure all required languages are updated

## Conclusion

This industry technology extension demonstrates the key principles of HOI4 modding: careful planning, consistent implementation, proper integration with existing systems, and thorough testing. The 60 new technologies provide a complete industrial progression from 1948 to 1990, filling the gap that existed in the original game while maintaining balance and historical authenticity.

The techniques shown here can be applied to any technology tree extension, whether for industry, military, or other research categories. The key is understanding the game's data structure, maintaining consistency with existing content, and creating an enjoyable player experience.

## Files Structure Summary

```
your_mod/
├── common/
│   └── technologies/
│       └── industry.txt (extended with 60 new technologies)
├── interface/
│   └── countrytechtreeview.gui (updated with new year displays)
├── localisation/
│   ├── english/
│   │   └── cold_war_l_english.yml (English tech names/descriptions)
│   └── german/
│       └── cold_war_l_german.yml (German tech names/descriptions)
└── descriptor.mod (mod metadata)
```

This structure provides a complete, functional extension to the Hearts of Iron 4 industry technology tree, serving as both a playable mod and a comprehensive example for aspiring modders.