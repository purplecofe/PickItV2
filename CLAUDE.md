# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

PickIt is a C# plugin for Path of Exile that automatically picks up items based on configurable filter rules. It's built as an ExileCore plugin using .NET 8.0 with Windows Forms support.

## Build Commands

**Prerequisites:**
- Set `exapiPackage` environment variable pointing to ExileCore installation directory
- .NET 8.0 SDK or Visual Studio 2022

```bash
# Build the project
dotnet build PickIt.sln

# Build for release (x64 platform)
dotnet build PickIt.sln -c Release -p:Platform=x64

# Build for debug
dotnet build PickIt.sln -c Debug
```

**Environment Variable Setup:**
```bash
# Windows (set permanently)
setx exapiPackage "C:\Users\user\Downloads\ExileApi-Compiled-3.26.last"

# Windows (current session only)
set exapiPackage=C:\Users\user\Downloads\ExileApi-Compiled-3.26.last

# macOS/Linux (for development)
export exapiPackage="/Users/chongkaihuang/Downloads/ExileApi-Compiled-master"
```

## Architecture

### Core Components

- **PickIt.cs**: Main plugin class inheriting from `BaseSettingsPlugin<PickItSettings>`. Contains the core picking logic, work mode management, and async picking operations.
- **PickItSettings.cs**: Configuration system with UI nodes for all plugin settings including hotkeys, ranges, toggles, and chest patterns.
- **PickItItemData.cs**: Wrapper around `ItemData` that adds pickup attempt tracking for ground items.
- **RulesDisplay.cs**: UI management for filter rules including loading, reordering, and applying `.ifl` filter files.
- **Misc.cs**: Inventory management utilities including space checking and item stacking logic.

### Key Dependencies

- **ExileCore**: Base framework for Path of Exile plugins
- **ItemFilterLibrary**: Handles `.ifl` filter file parsing and item matching
- **ImGui.NET**: UI rendering system
- **SharpDX**: Graphics and mathematics utilities
- **Newtonsoft.Json**: JSON serialization

### Filter System

The plugin uses `.ifl` (Item Filter Library) files located in the `Pickit Rules/` directory:
- Files are processed in order (priority-based)
- Each rule file contains item matching criteria
- Rules can be enabled/disabled and reordered via UI
- Custom config directories are supported via `CustomConfigDir` setting

### Work Modes

1. **Manual Mode**: Activated by PickUpKey hotkey or plugin bridge override
2. **Lazy Mode**: Automatic pickup when conditions are met (no enemies nearby, within range)
3. **Stop Mode**: Inactive when game window not focused or plugin disabled

### Inventory Management

- 12x5 grid inventory simulation
- Smart item stacking for stackable items
- Space availability checking before pickup attempts
- Real-time inventory visualization overlay

### Chest System

Configurable chest interaction with regex patterns for different chest types:
- Quest chests, League-specific chests (Expedition, Legion, Blight, etc.)
- Priority-based targeting (nearby chests first)
- Integrated with main pickup loop

## Development Notes

- Plugin uses async/await patterns with `SyncTask<T>` for non-blocking operations
- Caching system implemented for performance (labels, inventory states)
- Portal detection to prevent accidental clicks
- Mouse input simulation with configurable delays
- Debug highlighting available for testing item detection

## Configuration

Settings are managed through ExileCore's node system with automatic UI generation. Key configuration areas:
- Pickup behavior and hotkeys
- Inventory rendering and positioning
- Chest interaction patterns
- Lazy looting conditions
- Performance tuning (delays, ranges)