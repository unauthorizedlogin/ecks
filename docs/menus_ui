# 🖥️ UI Manager — Central Menu & Interface Authority

`UIManager` is the **central controller for gameplay interface systems**.

All major menus, panels, HUD references, and UI lifecycle management flow through this manager.

Individual UI components handle their own presentation logic, but global interface state, input routing, movement locking, and menu coordination are controlled centrally.

---

# 🎯 Core Responsibility

UIManager manages:

* UI registration
* menu lifecycle
* shared UI references
* global input routing
* movement lock state
* HUD visibility
* pause interaction
* UI-triggered quest events
* cross-system menu communication

Architecture:

```text
Gameplay Input
       ↓
 UIManager
       ↓
 Registered UI Systems
       ↓
 Individual Panels
```

---

# 📦 Managed Interface References

UIManager maintains centralized references to:

## Player Menus

```text
Inventory Panel
Equipment Panel
Storage Panel
Stats Menu
Quest Menu
Dialogue UI
Shop Menu
```

---

## HUD Systems

```text
Status Panel
MiniMap
Skillbar
Ammo Panel
Potion Panel
Event Viewer
```

---

## System Screens

```text
Pause Menu
Game Over Menu
```

---

# 🧩 Menu Registration Framework

Menus register through:

```gdscript
register_menu(menu)
```

Registered menus follow a shared contract:

```gdscript
open_menu()
close_menu()
```

This allows UIManager to control menus without knowing their internal implementation.

Example:

```text
             UIManager

                 |
 --------------------------------
 |        |        |       |
Inventory Shop   Quest  Dialogue
Equipment Storage Stats
```

---

# 🚪 Menu Lifecycle Authority

UIManager provides centralized closing:

```gdscript
close_menus()
```

This:

* closes registered menus
* restores gameplay state
* releases movement restrictions

Used during:

* scene transitions
* interaction cleanup
* pause transitions
* UI resets

---

# 🎮 Global Input Routing

UIManager handles global interface actions:

```text
ESC
Close Menus
Pause Menu
Toggle Stats
Toggle Event Viewer
```

Flow:

```text
Input Event
      ↓
 UIManager
      ↓
 Appropriate UI Action
```

This prevents individual panels from competing for global input ownership.

---

# 🔒 Gameplay Control Locking

UIManager manages gameplay interruption through:

```gdscript
movement_disabled
movement_enabled
```

Used by:

* menus
* dialogue
* pause
* UI text entry

Example:

```text
Open Inventory

      ↓

UIManager

      ↓

Player Movement Disabled
```

---

# 🗂️ UI State Tracking

Global UI state is tracked through:

```gdscript
is_open
```

This prevents gameplay input while menus are active.

Additional UI text state:

```gdscript
is_typing_ui
```

Allows input fields and rename operations to consume keyboard input safely.

---

# 📋 Quest System Integration

UIManager provides UI interaction notifications.

Example:

```gdscript
notify_menu_open("inventory")
```

Flow:

```text
Menu Opened
      ↓
UIManager
      ↓
QuestManager
      ↓
OPEN_UI Objective
```

Allows quests to track player interactions such as:

* opening inventory
* viewing menus
* interacting with interface systems

---

# 🛒 Shop System Integration

UIManager owns the active Shop Menu reference.

Initialization:

```gdscript
shop_menu = root.get_node(...)
register_menu(shop_menu)
```

Vendor systems communicate through:

```gdscript
UIManager.shop_menu
```

Responsibilities are separated:

```text
Vendor
  |
  ↓
ShopManager
  |
  ↓
UIManager.shop_menu
  |
  ↓
Shop Interface
```

Shop logic remains independent from UI presentation.

---

# 💬 Dialogue Integration

Dialogue UI is registered as a managed menu.

UIManager provides:

* opening/closing behavior
* movement locking
* global menu cleanup
* ESC integration

Dialogue content and conversation state remain controlled by the dialogue systems.

---

# 📢 Event Viewer Integration

UIManager manages Event Viewer visibility:

```gdscript
open_event_viewer()
close_event_viewer()
```

Supports:

* chat/event toggling
* independent HUD display
* global access from gameplay systems

---

# ⏸️ Pause System Integration

Pause flow:

```text
Input
 ↓
UIManager
 ↓
GameManager.pause_game()
 ↓
Pause Menu
```

UIManager handles:

* opening state
* movement restrictions
* menu coordination
* restoration of gameplay control

---

# ☠️ Game Over Integration

UIManager controls game-over presentation:

```gdscript
open_game_over()
close_game_over()
```

Handles:

* closing active menus
* disabling gameplay
* displaying recovery UI
* restoring player control

---

# 🧱 UI Architecture

```text
                    UIManager
                        |
 ------------------------------------------------
 |              |              |                |
 HUD          Menus        Dialogue          Shop
 |              |              |                |
MiniMap     Inventory       NPC Talk        Vendor
Skillbar    Equipment       Quests          Trading
Ammo        Storage
Potion      Stats
Event
```

---

# Framework Role

UIManager provides:

✅ Single UI authority
✅ Centralized menu lifecycle
✅ Unified open/close behavior
✅ Global input routing
✅ Player movement locking
✅ Quest OPEN_UI integration
✅ Shop interface coordination
✅ Dialogue menu management
✅ HUD visibility control
✅ Expansion point for future UI systems

The UI layer is structured as a coordinated framework subsystem rather than isolated panels, allowing new menus and interface systems to integrate through a consistent global manager.

For the architecture doc, I would keep the credits section focused on the **system role and capabilities**, not the implementation comments. Something like this:

---

# 🎬 Credits System — Dynamic Attribution & Presentation Layer

The Credits System provides a fully data-driven framework for displaying contributor information, music credits, legal attributions, special thanks, and dedication sections.

Credits are generated dynamically from external data rather than being hardcoded into the UI scene.

Architecture:

```text
Credits Data Resource
          ↓
Credits Root Controller
          ↓
Dynamic UI Generation
          ↓
Scrollable Credits Presentation
```

---

# 🎯 Core Responsibility

The Credits System manages:

* contributor listings
* department organization
* role/title presentation
* music attribution
* legal notices
* special thanks
* dedication sections
* visual formatting
* localization support

The system separates **credits content** from **credits presentation**.

---

# 📄 Data-Driven Credits

Credits content is loaded from:

```gdscript
credits_data_path
```

Default:

```text
data/templates/ui/menus/credits/credits_data.gd
```

The UI does not contain fixed names or entries.

Instead:

```text
Credits Data
      ↓
Credits Root
      ↓
Generated UI
```

This allows credits to be updated without modifying the scene.

---

# 🏗 Dynamic UI Generation

Credits are constructed at runtime.

The system generates:

```text
Credits Root
 |
 └── ScrollContainer
      |
      └── VBoxContainer
            |
            ├── Departments
            ├── Music
            ├── Legal
            ├── Special Thanks
            └── Dedication
```

Each section is created only when data exists.

---

# 👥 Department & Contributor System

Departments organize contributors into logical groups.

Example:

```text
Development
 ├── Developer
 ├── Programmer

Art
 ├── Artist
 ├── Animator

Design
 ├── Designer
```

Each contributor supports:

```text
Name
Role / Title
```

The system automatically builds formatted contributor rows.

---

# 🎨 Visual Customization

Credits presentation is fully inspector-driven.

Customizable:

## Contributor Names

```gdscript
name_font_size
name_color
name_outline_size
name_alignment
```

---

## Contributor Roles

```gdscript
title_font_size
title_color
title_outline_size
title_alignment
```

---

## Section Headers

```gdscript
section_title_font_size
section_title_color
section_alignment
```

---

# 🎵 Music Credits

Music attribution has its own presentation layer.

Supports:

```text
Track Title
Composer
Performer
Context
```

Example:

```text
Main Theme

Composed by: Artist Name
Performed by: Performer Name

(Battle Music)
```

Music styling is independently configurable.

---

# 📜 Legal Attribution System

Supports dedicated legal sections for:

* copyright notices
* licenses
* third-party assets
* attribution requirements

Displayed separately from contributor credits.

---

# 💖 Special Thanks

Dedicated section for non-development acknowledgements.

Supports:

```text
Name
Role / Description
```

Uses separate styling from standard contributors.

---

# 🙏 Dedication Section

Provides a final dedicated acknowledgement area.

Supports:

```text
Name
Optional Title
```

Designed for personal dedications or final messages.

---

# 🌍 Localization Support

Static section titles use localization:

```gdscript
tr()
```

Examples:

```text
Music
Legal / Attributions
Special Thanks
Dedicated To
```

Credits content can therefore support translated presentation.

---

# 🛠 Editor Preview Support

Credits Root runs in:

```gdscript
@tool
```

mode.

Changes to credits data can be previewed directly inside the editor.

The system refreshes periodically while editing, allowing visual iteration without running the game.

---

# 📐 Layout Control

Credits spacing is fully configurable.

Supports:

```text
Top spacing
Department spacing
Music spacing
Legal spacing
Thanks spacing
Dedication spacing
Bottom padding
```

This allows cinematic scrolling layouts without modifying UI scenes.

---

# 🧱 Framework Role

The Credits System provides:

✅ data-driven contributor management
✅ dynamic UI generation
✅ localization-ready presentation
✅ inspector-controlled styling
✅ music attribution support
✅ legal attribution support
✅ editor preview workflow
✅ reusable end-game presentation layer

The credits system functions as a standalone presentation framework that separates **content management**, **visual styling**, and **runtime generation** into independent layers.

Add this as a subsection under the Credits System architecture. This covers the generator pipeline specifically.

---

# ⚙️ Credits Data Generator — CSV → Runtime Credits Resource

The Credits Generator converts human-editable CSV credit data into a runtime-ready GDScript data resource.

This separates:

* credit authoring
* data organization
* runtime presentation

Architecture:

```text
Credits CSV
     ↓
Credits Generator
     ↓
credits_data.gd
     ↓
Credits Root UI
     ↓
Rendered Credits
```

---

# 🎯 Core Responsibility

The Credits Generator manages:

* CSV credit authoring
* section categorization
* department grouping
* contributor formatting
* music metadata generation
* legal attribution generation
* special thanks generation
* dedication generation
* duplicate cleanup

The runtime credits UI never reads CSV files directly.

---

# 📄 Source Data

Credits are authored through:

```text
data/generators/credits/credits_data.csv
```

CSV structure supports:

```text
Section
Department
Name
Role
Title
Composer
Performer
Context
Text
```

This allows all credit content to remain editable without touching UI code.

---

# 🔄 Generation Pipeline

When running inside the editor:

```gdscript
@tool
```

the generator automatically processes the CSV.

Flow:

```text
Editor Load
      ↓
Credits Generator
      ↓
Read CSV
      ↓
Organize Sections
      ↓
Generate GDScript
      ↓
Credits UI Loads Data
```

---

# 🗂 Credit Section Processing

The generator separates entries by section type.

Supported sections:

```text
department
music
legal
special
dedication
```

---

# 👥 Department Generation

Department entries are grouped automatically.

Example CSV:

```text
Department,Programming,Developer,Role
Department,Art,Artist,Role
```

Generates:

```text
Departments
 |
 ├── Programming
 |       └── Developer
 |
 └── Art
         └── Artist
```

Each department stores:

```gdscript
{
    "title": department_name,
    "members": []
}
```

---

# 🧹 Duplicate Protection

The generator removes duplicate contributors within departments.

Duplicate detection uses:

```text
Name + Role
```

Example:

```text
John Doe | Programmer
John Doe | Programmer
```

becomes:

```text
John Doe | Programmer
```

This prevents accidental repeated credits from appearing in the final display.

---

# 🎵 Music Credit Generation

Music entries generate structured metadata:

```gdscript
{
    "title": "",
    "composer": "",
    "performer": "",
    "context": ""
}
```

Supports:

* track names
* composers
* performers
* usage context

---

# 📜 Legal Attribution Generation

Legal entries are converted into localized text entries.

Generated:

```gdscript
"legal": [
    tr("Attribution Text")
]
```

Used for:

* licenses
* third-party assets
* copyright notices

---

# 💖 Special Thanks Generation

Special thanks entries generate:

```gdscript
{
    "name": "",
    "role": ""
}
```

Allows separate acknowledgement formatting from normal contributors.

---

# 🙏 Dedication Generation

Dedication entries support:

```gdscript
{
    "name": "",
    "role": ""
}
```

Designed for final acknowledgements and personal dedications.

---

# 🌍 Localization Integration

The generator automatically wraps translatable fields.

Generated examples:

```gdscript
tr("Credits")
tr("Department Name")
tr("Role")
tr("Context")
```

This keeps generated credit data compatible with the game's localization pipeline.

---

# 📦 Generated Output

The generator produces:

```text
data/templates/ui/menus/credits/credits_data.gd
```

Runtime access:

```gdscript
get_credits_data()
```

Returns:

```gdscript
Dictionary
```

containing:

```text
departments
music
legal
special
dedication
```

---

# 🧱 Framework Role

The Credits Generator provides:

✅ spreadsheet-style credit authoring
✅ automatic runtime resource generation
✅ section organization
✅ contributor grouping
✅ duplicate cleanup
✅ localization-ready output
✅ separation between content and presentation

The generator creates a maintainable credit pipeline where contributors can be managed as data while the UI remains a reusable presentation system.
