# Changelog

---

## [v0.52.198] - 2026-07-20

### Side Quest HUD

**System(s) Affected:** Quest System, Side Quest Tracker, Quest HUD

* Improved side quest tracking synchronization by aligning tracked quest updates with the standard quest selection flow.
* Added isolated HUD refresh handling so only affected tracked quests rebuild their UI state.
* Preserved existing tracked quest panels, objective displays, and active animations during tracking changes.
* Improved tracked quest ordering behavior when quests are added or removed.
* Added configurable tracking transition animations for side quest HUD updates.

---

## [v0.52.197] - 2026-07-19

### Private Repo Workflow

**System(s) Affected:** Private Repository, Development Workflow, GitHub Management

* Introduced a structured pull request workflow with categorized change tracking, implementation notes, validation checks, and documentation requirements.
* Added architecture-based system indexing to improve development organization and traceability.
* Created a GitHub label system aligned with the Ecks architecture and project folder taxonomy.
* Improved repository workflow consistency for future development and maintenance.

---

## [v0.52.196] - 2026-07-18

### Public Repo & SEO

**System(s) Affected:** Public Repository, Documentation, README, Changelog

* Expanded public repository documentation with remaining generator and database references.
* Increased public documentation coverage to over 70 architecture and system documents.
* Published and integrated the project changelog into the public repository.
* Refined README feature sections to prioritize direct documentation links and improve navigation.
* Updated repository links following documentation reorganization.

---

## [v0.52.195] - 2026-07-17

### Public Repo & SEO

**System(s) Affected:** Public Repository, Documentation, README, Changelog

* Expanded the public repository with remaining generator and database architecture documentation.
* Increased public documentation coverage to over 70 reference documents.
* Published the project changelog to the public repository.
* Updated README feature sections to prioritize direct documentation links over long explanations.
* Updated repository links after documentation reorganization and structural changes.
* Improved public framework navigation and accessibility for future users and contributors.

---

## [v0.52.194] - 2026-07-16

### Public Repo & SEO

**System(s) Affected:** Public Repository, Documentation, GameManager, Credits Menu, Dialogue UI, Shop System

* Updated the repository README into a clearer framework entry point for new users.
* Added menu screenshots to improve documentation visibility and framework presentation.
* Added direct navigation links to documentation directories for easier system discovery.
* Improved discoverability of individual framework systems and creator resources.
* Fixed a stale `title_screen` path reference in GameManager.
* Restored proper Credits Menu → Main Menu navigation flow.
* Fixed shop interactions for NPCs without dedicated `SHOP_GREET` flavor dialogue entries.
* Updated Shop NPC behavior to open correctly without requiring custom greeting dialogue.
* Removed duplicate NPC interaction toggles causing shops to immediately close after opening.
* Cleaned up the dialogue and shop interaction pipeline for more reliable NPC behavior.

---

## [v0.52.193] - 2026-07-15

### Public Repo & SEO

**System(s) Affected:** Public Repository, Documentation, Branding, Community Links

* Created the public Ecks repository to host architecture documentation and framework reference materials.
* Established a public identity for the framework while keeping development documentation separate from the main project.
* Built a consistent documentation foundation for future tutorials, roadmap updates, framework references, and public releases.
* Added cross-links between Ecks community platforms including itch, GameDev, Discord, and YouTube.
* Updated public-facing pages with consistent Ecks branding, descriptions, and reference links.

---

## [v0.52.192] - 2026-07-14

### Quest System Expansion

**System(s) Affected:** Quest System, Quest HUD Tracker, Side Quest Tracker, Combat Manager, VitalComponent, StatBlock, Quest Menu

* Refined Side Quest Tracker layout to support tracking up to three concurrent quests.
* Added configurable maximum objective limits to the main Story Quest HUD Tracker.
* Added independent animation configuration support for Side Quest tracker containers.
* Added `QUEST_KILL` objective tracking support.
* Implemented CombatManager kill event broadcasting through `target_killed(target, killer)`.
* Expanded combat objective resolution to support data from any combat participant, including PvE and PvP scenarios.
* Updated `VitalComponent.apply_damage()` to return kill results for combat event processing.
* Routed damage-over-time effects through CombatManager instead of directly modifying health values.
* Established StatBlock as the shared identity source for all combat actors.
* Updated QuestMenu as the authority for tracking limits and player quest tracking decisions.

---

## [v0.52.191] - 2026-07-13

### Quest System Expansion

**System(s) Affected:** Quest System, Quest Manager, Quest HUD Tracker, Side Quest Tracker

* Added `OPEN_UI` objective support for tracking player menu interactions.
* Added `QUEST_START` objective tracking for quest-driven progression flows.
* Added `QUEST_COMPLETE` objective tracking with completion routing through `QuestManager.complete_quest()`.
* Fixed quest line restrictions incorrectly preventing already-active quests from being tracked.
* Resolved cases where objectives appeared completed while the parent quest remained active.
* Added a configurable Side Quest HUD Tracker alongside the existing Story Quest Tracker.
* Separated quest HUD responsibilities by quest category to support multiple active quest types.
* Prevented side quest selection from overwriting the primary Story Quest tracker.
* Standardized Story and Side Quest trackers with shared UI customization support.

---

## [v0.52.190] - 2026-07-12

### Quest Menu Sync

**System(s) Affected:** Quest Menu, Quest HUD Tracker, Quest System, Dialogue UI, Localization, Autoload Systems

* Added `READY_FOR_TURN_IN` visual support to the Quest Menu, matching Quest HUD progression states.
* Updated quest objectives to display completed description labels while waiting for NPC turn-in.
* Unified quest objective state handling between Quest HUD and Quest Menu for a consistent player experience.
* Preserved completed objective history in the Quest Journal while maintaining active turn-in progression visibility.
* Resolved issues where `TURN_IN` objectives could remain stuck in the ready state after completion.
* Updated Quest Menu objectives to use the same icon-based state visuals as Quest HUD.
* Added inspector-configurable objective icons for improved customization and future UI themes.
* Audited and updated autoloader references after discovering unreliable `/root/` calls across systems.

### Dialogue UI Upgrades

**System(s) Affected:** Dialogue UI, Quest Dialogue, Flavor Dialogue, NPC Database

* Expanded Quest Dialogue support to fully handle all three progression states:
  * Start — Quest introduction and offer dialogue.
  * Active — Ongoing quest progression dialogue.
  * Complete — Quest turn-in dialogue before completion.
* Added data-driven quest button prefixes, allowing quest-specific button text without changing quest titles or requiring hardcoded labels.
* Added data-driven portrait support for both NPC and player speakers.
* Expanded Flavor Dialogue with dedicated `GREET` and `SHOP_GREET` dialogue states.
* Added automatic NPC greeting support when conversations begin or shops are opened.
* Improved dialogue header restoration by restoring NPC portrait, name, and title after conversations complete.

---

## [v0.52.189] - 2026-07-11

### Quest HUD Tracker

**System(s) Affected:** Quest HUD Tracker, Localization, UI Systems

* Added a dedicated `READY_FOR_TURN_IN` visual state to the Quest HUD Tracker.
* Updated objective rows to support independent visual states for Gated, Active, and Ready for Turn In stages.
* Expanded objective display clarity by separating quest progression states through dedicated styling.
* Completed a localization standards pass across custom UI labels and buttons.
* Updated approximately 100 custom label and button scripts to properly use `tr()` localization wrappers.
* Ensured custom UI entries now fully participate in the localization pipeline.

---

## [v0.52.188] - 2026-07-10

### Quest Building Support

**System(s) Affected:** Quest System, Quest HUD, ItemCountManager, Quest Objective Definitions, Quest Instance

* Introduced ItemCountManager as a universal access layer for item data and inventory counts.
* Updated item tracking to use inventory manager signals for live objective progress updates.
* Refactored Turn In objectives to operate independently from `COLLECT` objectives across all quest phases.
* Improved Turn In objective handling with dedicated completion and validation flow.
* Updated Quest HUD objective states to dynamically switch between active and completed states.
* Added objective gating support to control progression and unlock final quest objectives.
* Ensured Turn In buttons display correctly when quests reach the active turn-in state.
* Added `completed_description` support to all objective definitions for dynamic objective state labels.
* Updated QuestInstance quest state handling to ensure completed objectives display correctly in the Quest Journal.

---

## [v0.52.187] - 2026-07-09

### Quest Building Support

**System(s) Affected:** Quest System, Quest Objective Definitions, Shop Vendors, Item Removal System

* Continued expanding quest authoring support for the updated quest flow.
* Introduced new `PURCHASE` objective definitions.
* Refactored purchase handling into helper functions and connected purchase events to quest notifications.
* Updated Shop Vendors to use `shop_id` references, allowing quests to require purchases from specific vendors.
* Introduced new `TURN_IN` objective definitions for quest completion workflows.
* Integrated Turn In objectives with `COLLECT` requirements to support item return quests.
* Added ItemRemovalManager as a centralized routing system for item removal requests.
* Added `turn_in_npc_id` support to quest data for NPC-based item returns.
* Added `READY_FOR_TURN_IN` quest state handling to prevent turn-in objectives from auto-completing prematurely.
* Verified item removal flow is functioning correctly through the new quest pipeline.

---

## [v0.52.186] - 2026-07-08

### Dialogue UI Localization

**System(s) Affected:** Localization, Dialogue UI, Event Viewer, Quest Menu, NPC Database

* Completed localization audits for Event Viewer and Quest Menu systems.
* Added remaining missing localization strings for both menus.
* Began cleanup and restructuring of NPC data in preparation for the streamlined NPC resource system.
* Expanded NPC job title data with 404 new localized roles covering ranks, professions, and previously missing black market positions.
* Continued preparing NPC content pipelines for improved dialogue and character presentation support.

---

## [v0.52.185] - 2026-07-07

### Localization Audit

**System(s) Affected:** Localization, Dialogue UI, Event Viewer, UI Systems

* Began adding localization support for the Dialogue UI system.
* Audited the localization pipeline after discovering Godot Auto Translate was handling missing translations.
* Disabled Auto Translate during manual localization setup to restore consistent `tr()` wrapper usage.
* Reviewed existing menus affected by Auto Translate and updated missed localization labels.
* Expanded Event Viewer localization coverage by correcting previously untranslated UI elements.
* Identified that certain built-in controls, such as the Color Picker button, benefit from Auto Translate support.
* Established a hybrid localization workflow using manual `tr()` localization for custom menus while retaining Auto Translate where it improves built-in UI behavior.

---

## [v0.52.184] - 2026-07-06

### NPC Database

**System(s) Affected:** NPC Database, Dialogue UI, Resource Pipeline

* Converted the NPC generator from a script-based system into a resource-driven `.tres` pipeline.
* Introduced Resource Scanner and Autoloader support to align NPC data with the updated system architecture.
* Replaced quest-specific NPC handling with a dedicated `NPCData` resource structure.
* Expanded `npc_id` usage beyond quest targeting to support Dialogue UI data including display name, title, portrait, and speaker information.
* Converted NPC loading and access to use the new database architecture.
* Integrated Speaker ID handling to dynamically switch between NPC and player dialogue information.
* Resolved dialogue reset issues where conversations could incorrectly remain assigned to previous speakers.

---

## [v0.52.183] - 2026-07-05

### Dialogue UI

**System(s) Affected:** Dialogue UI, Quest Dialogue, Flavor Dialogue, Shop System

* Introduced the `OptionType` enum system for dialogue options:
  * `TALK`
  * `QUEST`
  * `SPECIAL`
  * `LEAVE`
* Replaced the single dialogue option container with a segmented UI layout.
* Added dedicated dialogue sections:
  * TalkButtons for Flavor Dialogue
  * QuestButtons for Quest Dialogue
  * SpecialButtons for Shop and future systems
  * LeaveButton for exiting interactions
* Reworked `_build_options()` into modular pipelines where each system independently contributes to the final dialogue options.
* Replaced flat option rendering with category-based rendering for improved scalability.
* Split dialogue startup into dedicated `start_flavor()` and `start_quest()` entry points.
* Prevented Quest Dialogue and Flavor Dialogue from incorrectly hiding or overriding each other.
* Added centralized `_cancel_pending_offer()` handling for quest offer cleanup.
* Improved quest offer flow so declined or ignored offers correctly clear from dialogue state.

---

## [v0.52.182] - 2026-07-04

### Dialogue UI

**System(s) Affected:** Dialogue UI, Quest Dialogue

* Updated Quest Dialogue flow to preserve the final dialogue line until the player provides an action input.
* Improved dialogue pacing by preventing the final line from immediately advancing or disappearing.

---

## [v0.52.181] - 2026-07-03

### Dialogue UI

**System(s) Affected:** Dialogue UI, Flavor Dialogue, Quest System

* Added a dedicated Flavor Dialogue resolver separate from Quest Dialogue handling.
* Enabled custom dialogue states for flavor interactions.
* Updated flavor dialogue ordering to follow resource-defined sequence order.
* Fixed Flavor Talk flow to properly trigger quest notifications.

---

## [v0.51.180] - 2026-07-02

### Dialogue UI

**System(s) Affected:** Dialogue UI, Character Master, Quest System, Flavor Dialogue

* Added Dialogue UI open/close toggle support through player interaction controls.
* Added NPC interaction range detection and automatic dialogue closing when leaving range.
* Expanded Flavor Dialogue resources with `name_label` support for customizable dialogue button names.
* Added configurable flavor dialogue button labels with `Talk` as the default option.
* Updated dialogue buttons to hide during active conversations to prevent duplicate interaction triggers.
* Added conversation progression controls through mouse input and Space Bar support.
* Added Quest Accept and Decline buttons that appear only during the appropriate quest decision stage.
* Completed functional dialogue interaction flow, with remaining layout improvements planned.
* Established the Dialogue UI system as the foundation for Side Quest integration and tutorial loop creation for the vertical slice/demo.

---

## [v0.51.179] - 2026-06-30

### Dialogue UI

**System(s) Affected:** Dialogue UI, NPC Database, Quest System, Shop System

* Built the Dialogue UI scene as the central NPC interaction hub for all NPC-driven actions including dialogue, quests, and shops.
* Created the Dialogue Option Button scene with automatic option generation support.
* Separated dialogue pipelines into Flavor Dialogue and Quest Dialogue systems.
* Connected NPC flavor dialogue through `dialogue_id` references from the NPC Database.
* Linked quests to NPCs through quest resource dialogue keys and matching `npc_id` values.
* Restricted quests to only appear on their assigned NPCs.
* Added quest availability checks preventing blocked chain quests and already active quests from appearing.
* Added automatic dialogue return flow after quest cancellation.
* Added Shop buttons to the Dialogue UI, replacing automatic vendor menu opening behavior.
* Expanded NPC presentation with names and titles, creating a foundation for future personality and character depth.

---

## [v0.51.178] - 2026-06-29

### Event Viewer

**System(s) Affected:** Event Viewer, Localization, Player Events, Quest System, Loot System, Combat System

* Resumed Event Viewer development and expanded runtime event presentation.
* Localized all player event emissions including shop, quest, loot, combat, and leveling events.
* Updated event localization handling to properly include dynamic variables within translated strings.
* Improved event readability by adding color formatting for important variables including item names, combat damage values, and quest names.
* Localized Event Viewer channel names including `QUEST`, `PLAYER`, `LOOT`, and other event categories.
* Fixed filter rebuild issues causing duplicated filter stacks when changing settings.
* Expanded localization support for XP gain and XP loss event emissions.

---

## [v0.51.177] - 2026-06-28

### Stat Effects

**System(s) Affected:** Stat Effects, Combat System, Dialogue UI, Resource Pipeline

* Resumed StatEffect array testing and expanded runtime effect support.
* Implemented OnHit effect processing.
* Implemented OnCrit effect processing.
* Added Knockback physics support for combat interactions.
* Began development of the Dialogue UI system.
* Created CSV-to-`.tres` pipeline support for Dialogue resources using Resource Scanner and Database Autoload architecture.
* Cleaned up StatEffect category and subcategory export handling by separating editor organization from generator folder routing.

--

## [v0.51.176] - 2026-06-27

### Stat Variables

**System(s) Affected:** Stat Variables, StatBlock, Formula Database, Resource Generators, Class Rules, Equipment System, Player Bootstrap Flow

* Created a `.tres` resource pipeline for stat formulas with dedicated Resource and Index Generators.
* Introduced FormulaDatabase API and FormulaResourceDatabase loaders for centralized formula access.
* Refactored StatBlock to receive derived stat variables from generated formula resources.
* Enabled all derived stats to be configured through the new formula resource directory.
* Separated resistance caps into dedicated `ResistRules` resources since they are rule-based rather than formula-driven.
* Updated formula resources to default with `NONE` type and `0.0` multipliers.
* Changed formula behavior so unused formulas are fully disabled instead of participating with forced zero multipliers.
* Added starting equipment configuration support to Class Rules resources.
* Expanded support for four additional custom equipment slots.
* Updated generators and CSV pipelines to support expanded stat formula and equipment data.
* Reworked Player Ready initialization into a dedicated Player Bootstrap Flow.
* Added startup flow protection for loading and starting equipment assignment.
* Moved new game save creation into the bootstrap process, preventing unnecessary saves during launches, loads, and map transitions.

---

## [v0.50.175] - 2026-06-26

### System Cleanup

**System(s) Affected:** UIManager, Settings Menu, Difficulty System, Launch Flow, Event Viewer, Stats Menu

* Disabled Settings Menu UI registration to prevent Start Menu ESC behavior conflicts.
* Fixed Difficulty Index loading through Resource Database to ensure difficulty resources export correctly.
* Cleaned up Launch Flow script variables and resolved unnecessary script warnings while maintaining functionality.
* Fixed Event Viewer Filter Panel rebuild behavior, preventing duplicate scaling and preserving filter colors during refreshes.
* Prevented Stats Menu from opening when using `Ctrl + C` copy actions inside Event Viewer.
* Added Event Viewer automatic scrolling with scroll interruption support, allowing users to pause scrolling by manually moving upward.

---

## [v0.50.174] - 2026-06-25

### Stats Cleanup

**System(s) Affected:** StatBlock, Stats Menu, VitalComponent, Credits

* Reintroduced Stamina as the primary resource stat, replacing Concentration as the active resource variable.
* Relocated Concentration into the Influencers category alongside Charm and Luck for expanded stat identity.
* Added new class identity stats including Fortitude, Willpower, and Barrier.
* Updated Credits UI integration to support the expanded stat system.
* Improved Stats Menu panel organization with grouped data sections and future scroll support.
* Added localization support and enum dropdown labels for new stats, menu titles, and stat headers.
* Resolved synchronization issues between Stats Panel scripts and VitalComponent by removing unnecessary panel-side interactions.
* Verified stats remain synchronized between the Stats Menu and HUD with no visual issues during equipment changes.
* Updated Credits generation pipeline to automatically generate `credits_data.gd`.
* Introduced CSV-based Credits generation with automatic grouping, sorting, and script creation.

---

## [v0.50.173] - 2026-06-24

### System Cleanup

**System(s) Affected:** Quest Objective Definition, QuestManager, Localization, Equipment UI, Pause Menu, UIManager

* Fixed `LEVEL_UP` objective definitions not updating correctly during progression.
* Added QuestManager synchronization helpers to display accurate level requirements on login (example: 6/9).
* Prevented automatic completion of level-based objectives when the player has not reached the required level.
* Fixed Equipment UI objective completion handling when requirements are successfully met.
* Localized Pause Menu Class and Difficulty cards, including class names, difficulty headers, and descriptions.
* Removed redundant calls from Equipment UI scripts to improve system cleanliness.
* Decoupled Pause functionality from registered menus, transitioning it into a state controller instead of a standard menu.
* Standardized ESC menu closing through UIManager registration, removing individual close handling from each menu.

---

## [v0.50.172] - 2026-06-23

### Equipment Flow

**System(s) Affected:** Equipment Manager, Equipment UI, StatManager

* Replaced equipment slot string references with enum-based slot management.
* Updated equipment slot naming from item types to body locations (example: Helm → Head).
* Added new equipment locations including Shoulders, Arms, and Waist slots.
* Improved equipment swapping behavior to prevent unintended inventory `add_item` calls during swaps.
* Refactored `equip_item` into helper functions for cleaner flow management and easier debugging.
* Fixed equipment flow issues that could cause item loss when inventory capacity was full.
* Resolved StatManager merge issues preventing item modifiers from applying correctly.
* Updated equipment UI slot selection to use enum-based dropdown configuration.

---

## [v0.50.171] - 2026-06-22

### System Recovery

**System(s) Affected:** Project Structure, Game Loop, Databases, Generators, Templates

* Refactored the Templates folder into a new Data structure containing Databases, Generators, and Templates.
* Improved project navigation by separating data systems into organized categories instead of a single large directory.
* Reconnected the complete gameplay loop from initial launch through credits and game over states.
* Verified core systems continue functioning correctly after the project structure migration.

---

## [v0.50.170] - 2026-06-21

### System Recovery

**System(s) Affected:** System Recover, Level Growth System, Difficulty System, Event Viewer, Project Structure, Level Database

* Restored Level Growth functionality by reverting to a stable commit and identifying the initialization issue affecting progression.
* Resolved a Difficulty System initialization loop in Player Ready that was preventing growth systems from completing setup.
* Restored working level progression and continued integration with the updated build architecture.
* Expanded Event Viewer coverage to capture live player events during runtime.
* Completed the project folder refactor and finalized the updated directory structure.
* Reintroduced Stamina system testing for Warrior progression support.
* Restored a stable development state after Level Database instability during progression updates.

---

## [v0.50.169] - 2026-06-20

### System Recovery

**System(s) Affected:** UI System, Level Growth System, Build Stability

* Restored project stability by returning to a known-good commit baseline.
* Continued forward development from a stable map system state.
* Cleaned up UI scripts by separating display responsibilities from system logic.
* Identified remaining Level Growth integration issues for future resolution.
* Established a cleaner foundation for continued system recovery and refactoring.

---

## [v0.50.168] - 2026-06-19

### Event Viewer

**System(s) Affected:** Event Viewer, Project Structure, Combat Manager, Economy Systems

* Continued project cleanup by refactoring and consolidating folder structure.
* Standardized the project organization into six primary folders:
  * Economy
  * Engine
  * Entity
  * Menus
  * World
* Expanded Event Viewer integration by wiring system initialization logs and runtime event emissions.
* Added the first permanent gameplay event source through Combat Manager.
* Implemented live combat logging with player damage output and critical hit detection display.
* Added live economy event tracking for purchases, sales, item pickups, and equipment acquisition.
* Established Event Viewer as an active runtime monitoring system with real gameplay data flowing through channels.

---

## [v0.50.167] - 2026-06-18

### Event Viewer

**System(s) Affected:** Event Viewer, UI Manager, Stats Menu, Quest Menu, Vendor Menu, World Structure

* Added Event Viewer open/close toggle support through UI Manager.
* Updated tab rename behavior to hide rename controls while the overlay is active.
* Resized the Event Viewer overlay layout to properly support the new toggle controls.
* Prevented Stats, Quest, and Vendor menus from opening while Event Viewer tab renaming is active.
* Converted level references to `map_id` references to resolve conflicts with player level data.
* Refactored project folder organization during system cleanup.
* Consolidated project structure into six primary folders:
  * Economy
  * Engine
  * Entity
  * Menus
  * World

---

## [v0.50.166] - 2026-06-17

### Event Viewer

**System(s) Affected:** Event Viewer

* Redesigned the Event Viewer architecture to move away from static tabs and forced filtering.
* Implemented a Default Tab view with support for user-created custom channels.
* Added independent filtering per channel while maintaining access to all available event data by default.
* Updated filtering architecture to dynamically generate available filters from the Filter Registry.
* Added configurable filter color selections for improved event readability.
* Established Filter Registry as the immutable source of filter definitions, with EventManager providing helper functionality and UI generating controls.
* Added customizable tab naming through LineEdit-based editing.
* Implemented Save to Config and Load from Config functionality to persist user Event Viewer settings.
* Reached baseline system stability and prepared the Event Viewer for live event integration.
* Began integrating timestamped event logging with millisecond precision for tracking system operations.
* Resolved mouse input leakage caused by previous tab renaming implementation.
* Localized Event Viewer interface elements including Main tabs, Custom tabs, Add, Rename, Delete, and standard filter controls.

---

## [v0.49.165] - 2026-06-16

### Effect System

**System(s) Affected:** StatEffect Resource, Effect Manager, Difficulty System, Entities, ResourceTeamDamage, Event Viewer, Combat System

* Refactored StatEffect modifier handling by separating FLAT and PERCENT behavior into a dedicated `STAT_MOD` structure.
* Updated Effect Manager architecture to support cleaner stat modification workflows.
* Updated Difficulty System effects to use the new StatEffect structure.
* Expanded Effect Manager support for Heal, Damage Over Time, and Heal Over Time effects.
* Added universal percentage conversion support through a toggle-based StatEffect setup.
* Converted Difficulty configuration from translator-based handling to the StatEffect system.
* Introduced StatEffect resource generation tools for automated effect creation.
* Created a reusable generator foundation that can support future Effect Library generation.
* Updated generated Difficulty effect resources to automatically populate Player and Enemy effect names.

* Implemented team filtering into the Combat System.
* Integrated team rules into Class Rules resources and connected access through Class Database data.
* Cleaned up the damage pipeline to prevent bypassing team validation.
* Verified team-based combat filtering and Friendly Fire overrides are functioning correctly.

* Introduced EventManager as the foundation for the Event Viewer system.
* Created an AoC-style in-game event panel designed to evolve into a future chat/debug interface.
* Added custom channels, manual filtering, automatic filters, and color filtering support.
* Established the Event Viewer as an in-game debugging tool without requiring editor access.

---

## [v0.49.164] - 2026-06-15

### Difficulty

**System(s) Affected:** Difficulty System, Engine, Entities, Boot Orchestration

* Created a new CSV-to-`.tres` pipeline for automated Difficulty configuration generation.
* Introduced Difficulty and Difficulty Resource Databases for centralized difficulty management.
* Added separate Player and Enemy difficulty multipliers for independent balancing control.
* Added configurable difficulty scaling for HP, Damage, XP, Loot, and AI behavior.
* Introduced BootManager as a centralized boot orchestration system.
* Implemented a 7-phase initialization order:
  * Databases
  * Registry
  * Progression
  * World
  * Economy
  * Player
  * UI
* Improved system initialization reliability by guaranteeing required data availability before dependent systems load.
* Established controlled startup sequencing for future system expansion.

---

## [v0.48.163] - 2026-06-14

### Miscellaneous

**System(s) Affected:** Map System, Map Generators, Map Resources, Class Database, Level Component

* Converted LevelManager from a script-based database into a resource-driven Level Database system.
* Introduced CSV-to-`.tres` generation pipeline for automated Level Data creation and management.
* Added LevelDatabase autoloader and LevelResourceDatabase registry scanning for runtime resource access.
* Expanded resource and index generators to support the updated database architecture.
* Upgraded ClassResourceDatabase and ClassDatabase to include XP Curve and Growth data.
* Added automatic class data attachment through Class ID references.
* Removed the previous forced `.tres` attachment workaround used after level switching.
* Improved level transition persistence by ensuring XP and growth data remain available across world changes.

---

## [v0.48.162] - 2026-06-13

### Miscellaneous

**System(s) Affected:** Equipment Manager, Inventory Manager, Quest System, Combat System

* Separated equipment responsibilities from InventoryManager into a dedicated Equipment Manager system.
* Moved equip, unequip, modifier application, validation, and serialization responsibilities into Equipment Manager.
* Simplified InventoryManager to focus exclusively on player inventory and storage management.
* Expanded Quest System event handling with support for 15 additional objective types.
* Verified equipment and level-up quest objectives are functioning correctly.
* Added Crit Chance and Crit Damage integration into the Combat System.
* Updated percentage-based calculations from 1000 rating scaling to direct 1:1 percentage values.
* Corrected combat calculations to use Damage values instead of Attack values.
* Verified Attack rating calculations now correctly determine average damage output.

---

## [v0.48.161] - 2026-06-06

### Shop Menu

**System(s) Affected:** Shop Vendor, Shop Manager, Shop Menu

* Resolved shop pricing issues where buy and sell values were incorrectly locked at 50%.
* Established proper price flow from Vendor configuration → Shop Manager → Shop Menu.
* Centralized shop price calculations to prevent duplicated logic across vendor, menu, and button scripts.
* Cleaned up shop pricing architecture and restored fully functional vendor-controlled pricing.

---

## [v0.48.159] - 2026-06-04

### Character Stats

**System(s) Affected:** Entities, BehaviorPatrol, EffectManager

* Removed hardcoded movement speed values from player movement.
* Integrated movement speed into the stat pipeline, making it a true derived stat.
* Updated NPC movement to pull speed values from the stat system instead of fixed values.
* Integrated NPC movement speed bonuses from equipment and effects.
* Fixed BehaviorPatrol NPCs breaking when spawned in chunk-loaded scenes.
* Resolved effects persisting after target death.
* Improved effect tick processing by preventing updates on dead or invalid targets.

---

## [v0.47.157] - 2026-06-02

### Multi System

**System(s) Affected:** ShopManager, Player HUD, StatBlock, VitalComponent

* Updated ShopManager to use `ItemResource.ItemType` through enum-based item classification.
* Added class-restricted item support through `required_class_id`, enabling class-specific equipment restrictions.
* Fixed Vital and Level Component persistence during level transitions.
* Resolved MP and Stamina HUD initialization issues displaying `0/0`.
* Updated VitalComponent initialization to properly load MP and Stamina values from StatBlock data.
* Added missing `mp_changed` and `stamina_changed` signal emissions during stat initialization.
* Improved Game Over screen handling through the UIManager-controlled modal system.
* Added a tick-based regeneration system to VitalComponent for runtime stat recovery.

---

## [v0.47.156] - 2026-06-01

### StatEffect

**System(s) Affected:** Effect System, StatEffect Resource, Effect Manager, StatManager, Equipment, Consumables, Potions, Entities

* Introduced the StatEffect → EffectManager → StatManager pipeline for centralized effect handling.
* Added StatEffect resource support for `apply_on_equip`, `apply_on_hit`, `apply_passive`, and `apply_on_consume` triggers.
* Expanded effect handling with support for:
  * Flat stat bonuses
  * Percentage-based scaling bonuses
  * Override values with controlled scaling
  * Damage over time and healing over time runtime effects
  * Revive effects with full control handling
* Verified OnEquip and OnConsume effects correctly apply to the owning entity.
* Added resource cost handling for equipment and consumables using flat and percentage-based modifiers.
* Integrated HP modification behavior to correctly interact with damage and death states.
* Established StatEffect as a universal stat distribution framework for future systems including skill nodes, enchantments, stat potions, and other modifiers.

---

## [v0.46.155] - 2026-05-31

### Item System

**System(s) Affected:** Item Generators, Item Rules, Class System

* Brought the Item Content Generator online for automated item resource creation.
* Introduced the Item Resource Database architecture for centralized item data management.
* Added Item Resource and Index Generators to support scalable generated item content.
* Updated the ItemDatabase autoloader and retired legacy database scripts.
* Enabled item stats to be configured directly through the Inspector, matching character stat workflows.
* Added item level requirements to equipment rules.
* Expanded item rules with class type restrictions, allowing equipment to be limited by class identity.
* Improved gear customization by ensuring class restrictions apply independently from equipment weight or category.

---

## [v0.46.155] - 2026-05-31

### Class Rules

**System(s) Affected:** Class System, Item Resource

* Brought the Class Rules Generator online for automated class restriction and customization setup.
* Added class-based gear rules to control equipment availability and customization options.
* Expanded class identity beyond base stats by making class selection impact usable gear.
* Converted class item rules into enum-based configuration for improved resource management and editor support.

--

## [v0.46.154] - 2026-05-29

### Indexing Generators

**System(s) Affected:** Indexing System, Resource Databases

* Introduced Indexing Generators to automate runtime script creation for generated resources.
* Established indexing as the standard solution for all `.tres` generated database systems.
* Resolved export limitations where generated resource files were not reliably available at runtime.
* Added script-based resource references loaded through autoloaders to ensure database availability after export.
* Fixed generated UI workflows relying on indexed resources, including automatic class selection button generation.
* Updated resource database architecture to support reliable loading for systems such as Quests and future generated content.

--

## [v0.45.151] - 2026-05-26

### Combat System

**System(s) Affected:** Combat System, NPC

* Introduced CombatManager as the foundation for centralized combat handling.
* Added CombatResolver as the combat stat authority for resolving combat calculations.
* Implemented CombatContext, CombatRequest, and CombatResult structures for standardized combat interactions.
* Added CombatVitalComponent to build combat profiles from StatBlock data.
* Created character enemy registration and damage handling through the enemy combat script.
* Implemented damage calculation and application using derived stats from the stat system.
* Resolved issue on NPC enemy that was duplicating VIT bonuses

--

## [v0.44.148] - 2026-05-23

### Quest Matrices

**System(s) Affected:** Quest Matrix, Quest HUD, Quest Progression, Quest Generators

* Built the Quest Matrix system to define rules and behaviors for different quest categories.
* Added new quest categories: `STORY`, `SIDE`, `DAILY`, and `CHAIN`.
* Upgraded quest UI dependencies from direct quest state checks to category-based behavior.
* Built the Quest Progression Layer to support structured progression paths.
* Introduced ProgressionManager for centralized progression tracking and reward routing.
* Added progression domains including `CLASS`, `FACTION`, `JOB`, and `SKILL`.
* Implemented reward routing support for XP and reputation rewards across progression domains.
* Expanded quest CSV data support with new fields for quest category, progression domain, progression ID, XP rewards, and reputation rewards.
* Updated CSV loading to use header-based field mapping instead of fixed column positions, allowing easier data organization and expansion.

--

## [v0.43.146] - 2026-05-21

### XP System

**System(s) Affected:** Quest Menu, Player HUD, XP Growth Generator, StatManager, InventoryManager

* Connected Quest Menu and Player HUD reward displays to the XP reward pipeline.
* Created CSV-based level growth importing for automated progression curve generation.
* Introduced StatEffectManager as the centralized authority for stat modifiers and gameplay effects.
* Refactored InventoryManager to use the new stat effect architecture, reducing unnecessary load and improving system separation.
* Established a cleaner stat architecture:
  * StatEffectManager manages all modifiers and stat changes.
  * InventoryManager manages item and equipment state only.
  * StatBlock handles base stats and derived stat calculations.
* Updated `ui_apply_mods` to follow the new stat architecture and removed modifier authority from the UI layer.
* Verified stat application, persistence, and equipment changes correctly survive equip/unequip cycles and level transitions without duplication.
* Integrated Level Growth and Equipment systems into the updated progression pipeline.

--

## [v0.43.145] - 2026-05-20

### XP System

**System(s) Affected:** XP Manager, XP Curve Generator, XP Simulation Analyzer

* Introduced the XP Manager system for centralized experience tracking and progression management.
* Created the XP Curve/Progression Balancer plugin with debugging tools for testing progression balance.
* Added CSV-based XP curve generation with baked-in progression formulas.
* Implemented XP analysis, simulation, graphing, and stress testing tools to validate progression curves.
* Expanded the XP balancing workflow with interactive UI controls for simulations and visual analysis.
* Established a functional XP progression testing environment, with additional debugger panel enhancements planned.

--

## [v0.43.143] - 2026-05-18

### Quest HUD Upgrades

**System(s) Affected:** Shop Menu, Quest HUD, Quest Menu, Localization

* Fixed pause state leakage preventing the Shop Menu from opening incorrectly during paused states.
* Updated Quest HUD objective alignment to begin from the center position for improved readability and presentation.
* Added state dependency checks to Quest Menu Track/Cancel Quest buttons.
* Created Quest Track button enum labels and added 87 new localized strings.
* Created Quest Cancel button enum labels and added 60 new localized strings.

--

## [v0.43.142] - 2026-05-17

### Quest HUD Upgrades

**System(s) Affected:** Quest HUD, Quest Menu, Quest Manager

* Expanded Quest HUD support from a single objective display to multiple dynamic objectives.
* Synchronized Quest HUD tracking with Quest Menu selection.
* Added Accept/Track functionality to replace automatic quest selection behavior.
* Prevented completed quests from incorrectly appearing as active tracked quests.
* Improved quest deserialization by rebuilding objective definitions from source data instead of saving them directly.
* Increased quest stability when objective structures are modified after quests have already been created.

--

## [v0.43.140] - 2026-05-15

### Quest System Stable

**System(s) Affected:** Quest Generators, QuestManager, Quest Menu, Shop Menu, Localization

* Expanded quest data generation to support `quest_line_id` and `quest_index` values.
* Added quest line support to QuestManager, allowing quests to be grouped and progressed through structured sequences.
* Updated quest data pipelines to support `objective_gate` requirements.
* Added gated objective support to the Shop Menu flow.
* Updated CSV loading pipelines to import new quest columns while maintaining compatibility with existing data.
* Confirmed quest line progression and objective gating flows are functioning correctly.
* Converted Quest Menu and Shop Menu into CanvasLayers to properly respect pause states and maintain persistence.
* Added Quest Menu title label configuration through enums and export lists.
* Added 143 new localized strings covering Quest Titles, Quest Names, Descriptions, and Objectives.

--

## [v0.42.139] - 2026-05-14

### Quest Progress Updating

**System(s) Affected:** Quest Objective Definitions, Shop Vendor, NPC Database, Quest Instance, Quest HUD, Quest Rewards

* Connected interaction systems to quest notifications, confirming `TALK` objectives update correctly.
* Updated Shop Vendors to communicate with the quest pipeline for testing and integration.
* Introduced the NPC Database system using `npc_id` references for NPC identification and data management.
* Added Interact ID exporting to Dynamic Chest scenes through Quest Instance integration.
* Updated Quest HUD instance handling with new `handle_talk` and `handle_interact` functionality.
* Added quest completion UI states with check marks, green text, and completed quest filtering.
* Resolved template Tilemap corruption by implementing system backups.
* Completed Quest Reward integration for Potions, Currency, Arrows, Bombs, and Equipment systems.

--

## [v0.42.138] - 2026-05-13

### Quest Progress Updating

**System(s) Affected:** Quest HUD, Quest Objective Definitions

* Updated the Quest HUD to display objective requirements instead of raw integer values.
* Ensured quest objectives correctly respect the `item_id` database structure.
* Confirmed quest objective localization is functioning correctly.
* Connected pickup systems to quest notifications, confirming `COLLECT` and `TALK` objectives update correctly.
* Fixed objective progress display so counts update dynamically instead of remaining stuck at `*/1`.
* Confirmed quest progress saves correctly and persists between worlds without losing progress.

---

## [v0.42.137] - 2026-05-12

### Quest System Implemented

**System(s) Affected:** Quest Manager, Quest Database, Quest Generators

* Implemented the CSV-to-Quest data pipeline.
* Quest data now loads correctly during boot.
* QuestManager is fully operational with real quest data.
* Brought the Quest UI online with responsive quest data display and updates.
* Connected the quest pipeline from data authoring through runtime management and UI presentation.
* The complete quest pipeline is now operational through the quest completion phase.

---

## [v0.41.136] - 2026-05-11

### State Isolation, Pause Leak Resolution, and Window Close Standardization

**System(s) Affected:** Game Manager, Launch Flow

* Integrated the `MENU`, `LOADING`, `PLAYING`, and `PAUSE` game states throughout the boot-to-game launch flow.
* Hardened pause behavior so it can only be activated while the game is in the `PLAYING` state.
* Standardized the New Game, Load Game, Save Game, and Settings windows with unified close-request handling.
* Added consistent window closing through both `Esc` and the window `X` button.
* Updated world rebuilding to restore the player using their saved position instead of default spawn points.


---

## [v0.41.126] - 2026-05-02

### Engine Migration and Level ID System

**System(s) Affected:** Engine, Save System, Shop System, Map System, World Injector, Chunk Manager

* Migrated the project to Godot 4.5, exposing and resolving stricter execution-timing requirements.
* Migrated the World Injector to Godot 4.6.2.
* Implemented a Level ID system for reliable world and level identification.
* Improved level switching and world persistence across transitions.
* Fixed new-game initialization so `current_save_data` is available before systems attempt to save.
* Prevented the Shop System from triggering saves before save data has been initialized.

---

## [v0.40.116] - 2025-08-25  
**Description Library Expansion**  
- T1 Class Weapons | Added 27 new entries | New total: 11,627 entries (267,421 localized strings)  
- Generic Weapons | Added 667 new entries | New total: 12,294 entries (282,762 localized strings)  
- Generic Accessories | Added 206 new entries | New total: 12,500 entries (287,500 localized strings)  
- Uniques | Added 11 new entries | New total: 12,511 entries (287,753 localized strings)  
- Expanded item descriptions | Provide clarity, maintain consistency, and improve overall library coverage for weapons, accessories, and unique items

---

## [v0.40.115] - 2025-08-24  
**Description Library Expansion**  
- Stones | Added 1,008 new entries | New total: 10,104 entries (232,392 localized strings)  
- Minerals | Added 118 new entries | New total: 10,222 entries (235,106 localized strings)  
- Crafting Materials | Added 177 new entries | New total: 10,399 entries (239,177 localized strings)  
- T1 Class Armor | Added 102 new entries | New total: 10,501 entries (241,523 localized strings)  
- T2 Class Armor | Added 90 new entries | New total: 10,591 entries (243,593 localized strings)  
- Generic Armor | Added 1,009 new entries | New total: 11,600 entries (266,800 localized strings)  
- Standardized and enriched descriptions | Improve readability and consistency across the library

---

## [v0.40.114] - 2025-08-23  
**Armor Description Expansion**  
- Prepared and standardized descriptions for generic armor  
- Converted entries to new format | Example:  
  - Before: `ARMOR_DRAGONBONE_BOOTS` — Crafted from Dragonbone  
  - After: `DESC_ARMOR_DRAGONBONE_BOOTS` — Boots forged from Dragonbone, offering exceptional durability and protection  
- Applied this pattern across 1,009 armor items  
- Provides a reference for typical description style in the item library

---

## [v0.40.113] - 2025-08-23  
**Potions & Status Effects**  
- Added 63 new entries for Potions  
- Added 65 new entries for Status Removal Potions  
- Added 65 new entries for additional Potions  
- New Total | 7,086 entries (162,978 localized strings)  

**Descriptions Expansion**  
- Added 124 entries for Potion descriptions  
- Added 286 entries for generic heal/restore/replenish % descriptions  
- Added 42 entries for Potion-specific descriptions  
- Added 65 entries for Status Removal Potion descriptions  
- Added 100 entries for Ammo descriptions  
- Added 65 entries for Currencies descriptions  
- Added 78 entries for Food descriptions  
- Added 37 entries for Drink descriptions  
- Added 47 entries for Snack descriptions  
- Added 131 entries for Fruit descriptions  
- Added 128 entries for Vegetable descriptions (part 1)  
- Added 148 entries for Vegetable descriptions (part 2)  
- Added 66 entries for Pickle descriptions  
- Added 152 entries for Pepper descriptions  
- Added 94 entries for Condiment descriptions  
- Added 27 entries for general Toppings descriptions  
- Added 168 entries for Primary Gems descriptions  
- Added 224 entries for Secondary Gems descriptions  
- New Total | 9,068 entries (208,564 localized strings)  

**Stones**  
- Added 28 new entries for STONE_ (Jade tiers)  
- New Total | 9,096 entries (209,208 localized strings)

---

## [v0.40.112] - 2025-08-21  
**Discord Updates & Recruitment**  
- Updated Discord recruitment posts | Highlighted completed systems, features, and ongoing work  
- Posts now reflect both system-specific and chronological patch updates | Improves clarity for the community  

**Localization / Gems Update**  
- Added 112 new GEM_ entries | Completed Birthstones set: Alexandrite, Pearl, Tanzanite, Turquoise; added to secondary Gems category  
- Library Total | 6,942 unique keys / 159,666 localized strings  
- Added 16 new GEM_ entries | Completed sets for Citrine, Opal, Tourmaline, Zircon; included variants: Chipped, Flawed, Flawless, Perfect  
- Library Total | 6,958 unique keys / 160,034 localized strings

---

## [v0.40.111] - 2025-08-20  
**GitHub - VS Code AI Integration**  
- Installed and configured GitHub Copilot in VS Code | Assists with debugging map rehydration issues  
- Repository now more directly accessible to AI tooling | Streamlines progression and troubleshooting  
- Initial debugging attempts unsuccessful | Setup provides foundation for future AI-assisted problem solving  

**Outcome**  
- First step in integrating AI coding assistance directly into the workflow  
- Next phase | Refine prompts, test AI suggestions, and iterate on the rehydration system


---

## [v0.40.110] - 2025-08-19  
**Systems Tracker Refactor**  
- Completed full refactor of the systems tracker (milestone sheet)  
- Updated old milestones and changelog entries | Ensures they reflect current development status  
- Original milestone sheet remains unchanged | Maintains chronological patch note tracking  
- Relevant milestones copied into new systems tracker | Associated with their respective systems  

**Outcome**  
- System-specific tracking | Enables detailed updates and pending features  
- Chronological tracking | Preserves all patch notes for historical reference  
- Improved clarity and separation between patch log and system-specific milestone tracking  
- Faster reference for development progress and historical changes without losing context

---

## [v0.40.109] - 2025-08-18  
**Milestone Sheet Overhaul**  
- Reorganized Milestone Sheet | Improved clarity and progress tracking in Excel tracker  
- Updated all systems to reflect current development status  
- Added Pending Features column | Captures upcoming tasks and polish goals  

**Highlights of Updated Systems**  
- Inventory (v0.2.02) | 64 inventory slots + 11 equipment slots, drag & drop, modifiers applied, chest routing  
- Item Databases (v0.3.03) | Accessories ✅, groundwork for Ammo, Currency, Consumables, Equipment, Potions, Tools, Quests, Uniques, Weapons  
- Dynamic Spawning Chests (v0.4.04) | Functional chest system, rarity tables pending  
- World Systems | Tilemap physics layers, y-sorting, Weather FX, Environmental Audio, Dynamic Chunk Loading (4x4 grid)  
- Core Gameplay | Stats, Equipment, Consumables, Patrol & Behavior AI, Destroyables, Teleportation, Derived Stat system resolved  
- UI & Managers | Item Creator Plugin, UI Manager, Game Manager Autoloader, Transition Manager, Save/Load System  
- Framework & Modularity | Templates Layer for Characters, Items, Maps, and UI Managers  
- Content | End Credits scene, Character Selection (12 classes), Shop Menu setup  

- ✅ Completed systems marked; Pending Features highlight next steps

---

## [v0.40.108] - 2025-08-17  
**Dictionary Expansion**  
- Added 27 new entries for Ingredients, Minerals, Stones to the localization dictionary  
- Added 53 new entries for Gems to the localization dictionary  
- Added 804 new entries for Stones to the localization dictionary  
- Added 17 new entries for Metal to the localization dictionary  
- Added 148 new entries for Armor to the localization dictionary  
- Added 112 new entries for Weapon to the localization dictionary  
- Added 40 new entries for Minerals to the localization dictionary  
- Library Total | 6,830 unique keys (157,090 localized strings)

**Database Standardization**  
- Standardized the order of all item databases | Name, Price, Rarity, Type, Item Type, Stackable, Description, Variables (Class, Equip Slot, Effects, Modifiers)  
- Ensures future additions and edits remain structured and easily trackable

---

## [v0.40.107] - 2025-08-16  
**Metals & Gems Serialization / Save/Load System**  
- Added missing values in SaveData Resource | `metal_data` and `gem_data` dictionaries now track quantities  
- Serialization system for metals and gems is fully functional  
- ⚠️ Next Steps | Inventory locations for metals and gems still need to be created

**Expanded Item Categories & Shop Testing**  
- Added remaining item categories to the item database | Alphabetized for easier tracking  
- New entries added for Ingredients, Minerals/Stones, Toppings (27 new entries; Total: 5,656 | 130,088 localized strings)  
- Created new NPCs to test new item categories and verify purchasing functionality | Purchasing confirmed functional

**Diamond Gem Expansion**  
- Created Diamond gem list | Matches Amethyst, Emerald, Ruby, Sapphire, Topaz  
- Expanded gem tiers | Chipped, Flawed, Flawless, Perfect; total 28 variants per gem  
- Added 53 new GEM_ entries | New Database Totals: 5,709 entries | 131,307 localized strings

**Expanded Stones, Metals, Armor, and Weapons**  
- Stones | Added 28 new STONE_ entries for missing tiers, 216 new witch stone entries, 560 new stone type entries with all 28 tiers  
- Metals | Added 17 new METAL_ entries including variants for Copper, Nickel, Tin, Titanium  
- Armor | Added 148 new ARMOR_ entries with updated metal variants  
- Weapons | Added 112 new WEAPON_ entries with updated metal variants  
- New Localization Totals | 6,790 entries | 156,170 localized strings

---

## [v0.40.106] - 2025-08-15  
**Massive Dictionary Expansion - 1794 New Keys**  
- Added 204 new entries for Accessories to the localization dictionary  
- Added 861 new entries for Armor to the localization dictionary  
- Added 84 new entries for T1 Class Gear to the localization dictionary  
- Added 90 new entries for T2 Class Gear to the localization dictionary  
- Added 555 new entries for Weapons to the localization dictionary  
- Library Total | 5,629 unique keys (129,467 localized strings)

**Item Expansion**  
- Created new Autoloaders for new item types | Gems Manager, Ingredients Manager, Metals Manager, Minerals Manager, Stone Manager  
- Integrated new item types into Shop Manager  
- Integrated new item types into Save Manager & SaveData  
- Integrated new item types into Game Manager

---

## [v0.40.105] - 2025-08-14  
**Dictionary Expansion - 332 New Keys**  
- Added 66 new entries for Pickles to the localization dictionary  
- Added 152 new entries for Peppers to the localization dictionary  
- Added 98 new entries for Condiments to the localization dictionary  
- Added 8 new entries for shop titles for gems/jewels  
- Added 8 new entries for shop inventory related to gems/jewels  
- Updated enum lists | Shop title and shop inventory  
- Alphabetized the shop title enum list  
- Library Total | 3,742 unique keys (86,066 localized strings)

---

## [v0.40.104] - 2025-08-13  
**Dictionary Expansion - 1104 New Keys**  
- Added 94 new entries for Metals to the localization dictionary  
- Added 109 new entries for Minerals to the localization dictionary  
- Added 384 new entries for Gems to the localization dictionary  
- Added 131 new entries for Fruit to the localization dictionary  
- Added 128 new entries for Vegetables to the localization dictionary  
- Added 163 new entries for Herbs/Spices to the localization dictionary  
- Added 95 new entries for Ingredients to the localization dictionary  
- Library Total | 3,410 unique keys (78,430 localized strings)

---

## [v0.40.103]  
**Shop Menu - Vendor NPC Label Control & Refactor**  
- Expanded shop menu label system | Controlled directly by vendor NPC (character_vendor.gd) instead of individual label nodes
- Consolidated enum label lists | Shop Titles, Shop Inventory Labels, Sell Inventory Labels, Buyback Labels, Info Labels
- Each label enum | Exported int property for preset selection, custom label text support, toggle between enum/custom, automatic text update functions
- Vendor NPC control | Shop title, inventory, sell, buyback, and info labels managed centrally; monitors player proximity to open/close shop UI
- Vendor inventory built dynamically | From NPC's item_ids list
- Improved shop interaction logic | Simplified opening/closing from vendor node
- Abandoned previous modular label node approach | For simplicity and reliability
- System supports easy expansion | New shop types and label variations centralized
- ⚠️ Known Issues | Polish and edge cases ongoing; core label communication now resolved

---

## [v0.40.102] - 2025-08-11  
**Shop Menu Improvements & Testing**  
- Implemented open() function | Initializes shop UI with vendor inventory and dynamic labels
- open() parameters | Vendor inventory array, shop title, player node, optional label texts for shop, sell, buyback inventories, and info labels
- Labels set immediately on UI nodes using localization (tr())
- UI lists (shop, sell, buyback) updated after setting labels
- Button nodes (Buy, Sell, Buyback) | Dynamically connected to signal handlers; inventory and list references assigned on open; Buyback button hidden by default
- ⚠️ Known Issues | Experiencing label communication and dynamic updating issues; investigation ongoing

---

## [v0.40.99] - 2025-08-09  
**Chunk Serialization & Loader Safety Improvements**  
- Added serialize_chunk_state() in ChunkManager | Supports saving chunk destruction states in any scene
- Updated init_chunk_loader() in ChunkManager | Added safety guard to prevent multiple initializations
- Made get_player() retrieval scene-safe and dynamic
- Added robust type and empty checks in serialization | Avoids errors when no chunks are loaded
- ⚠️ Known Issues | Loading chunks from in-game saves does not restore correctly; linked to init_chunk_loader() calls and map loading sync
- ⚠️ Next Steps | Trace and fix chunk load state restoration; align chunk loader initialization with current map and camera references; implement scene-aware save/load for correct resume

---

## [v0.40.97] - 2025-08-05  
**Chunk Loader Integration & Level Switch Troubleshooting**  
- Updated ChunkManager.init_chunk_loader() | Now accepts settings passed from a dedicated ChunkLoader script; supports custom paths via template injection
- Updated chunk_loader.gd | Added export variables: target_node_path, chunk size, radius, bounds, chunk scene/data path templates, LOD distances
- Delayed init_chunk_loader() call | Uses await get_tree().process_frame to ensure exports are set
- Integrated get_map() and get_camera_grid() from the world scene into Chunk Manager
- ⚠️ Still encountering errors | Environment re-injection and full map regeneration during world switching is broken; cleanup and debugging ongoing

---

## [v0.40.94] - 2025-08-02  
**Bug & Polish Pass**  
- Updated Shop Menu | Updated player inventory on pickup, buy, sell, and buyback
- Cleaned up statblock bug | No more duplication when using character class stats
- Got LOD working with new chunk manager setup.
- Ready for refactoring to be used with Chunk Loader.
- Fixed language dropdown bug for Portuguese and Norwegian
- Switched locale codes in Language Manager to nb and pt
- Translations are still done in no and pt-BR

---

## [v0.40.88] - 2025-07-27  
**Shop Vendors**  
- Implemented modular shop menu that loads items via item IDs
- Integrated buy/sell logic with save triggers
- Prices now sourced directly from item databases
- Selling auto-calculates item value at 50% of purchase price
- Shop manager autoload routes inventory data to proper managers
- Pending: Sell list doesn't refresh after purchases (UI quirk)

---


## [v0.39.81] - 2025-07-20  
**Templates System**  
- Completed full system-wide refactor to use system → templates → content structure
- Extended item master to templates, with inherited types for ammo, armor, food, potions, weapons, and more
- Resolved inheritance issues with pickup scripts and player type casting
- Built and tested consumables, armor, and weapons; serialization and pickup fully functional
- Extended maps to templates, migrated chunk system, verified LOD and loading for all 48 chunks
- Game Manager updated to use new template-driven chunks
- Marks milestone: Templates System v1 — stable foundation for modular content production

---

## [v0.38.77] - 2025-07-16  
**Character Selection**  
- Established character selection in the new game window  
- Pulls from existing class database and stat block setup  
- Automatically applies stats on login  

---

## [v0.37.72] - 2025-07-11  
**End Credits**  
- Created new End Game Zone  
- Created credits_root scene housing credits_departments scene with automated control script  
- Exported variables and separated scroll controls to its own node  
- Made scene live @tool for editor preview and automatic updates without launching the game  

---

## [v0.36.71] - 2025-07-10  
**Serialization & Game Over Loop**  
- Completed full save/load system: player, chunk, inventory, storage, equipment, ammo, potion slots  
- Completed Game Over scene with restart, load from checkpoint, and 16 new translations  
- Created autosave/checkpoint and death zones placeable in the tilemap world  

---

## [v0.35.59] - 2025-06-28  
**Version Control**  
- Installed Git and Visual Studio Code for local development  
- Created GitHub account and repository  
- Synchronized local and remote repositories  

---

## [v0.34.59] - 2025-06-28  
**Settings Menus**  
- Composed Settings Menu scene for Pause and Start Menus  
- Introduced Settings Manager saving user config settings (all working except vsync)  
- Introduced Save Manager with autosave, quicksave, and 10 manual save slots  
- Load and save communicate and auto-update each other  
- Added confirm dialogs for quit, restart, delete, overwrite  
- Fully localized UI  

---

## [v0.33.59] - 2025-06-28  
**Game Manager**  
- Manages game states (MENU, PLAYING, PAUSED, CUTSCENE, LOADING)  
- Tracks session data: player name, level, deaths, difficulty, playtime  
- Handles scene loading with timer-based transitions  
- Emits signals for state changes, loading progress, and level changes  
- Supports pause/resume, menu return, restart, level progression  
- Includes debug tools for skipping levels and forcing states  

---

## [v0.32.58] - 2025-06-27  
**Localization**  
- Support for 22 languages with dropdown in title screen and start menu dynamically changing strings  
- CSV to PO file conversion tool facilitates easy translation management  
- Language manager autoloader and bootloader control  

---

## [v0.31.56] - 2025-06-25  
**CSV to PO File Generation Tool**  
- Built an in-editor CSV to PO conversion tool for seamless translation updates  
- Parses CSV files and updates PO files for all 20 supported languages  
- Avoids cloud dependencies by using local CSV editing with LibreOffice  

---

## [v0.30.52] - 2025-06-22  
**Environmental Audio System**  
- Created AudioZone base class  
- Imported 25 environmental audio files covering wind, rain, waves, river, waterfall, quakes, cave drips, darkness ambience, villagers, crickets  

---

## [v0.29.41] - 2025-06-09  
**Consumables System Expansion**  
- Added StatEffect resource system  
- Introduced test consumables: Pizza (flat stat boost), Soda (speed or stamina boost), Ice Cream (charm, luck, temp regen)  
- Added new SFX for potion and food consumption  

---

## [v0.28.38] - 2025-06-06  
**Item Creator Plugin & UI Refactor**  
- Developed in-editor Item Creator plugin for mass creation of items  
- Environment injection and scene refactor improvements  

---

## [v0.27.38] - 2025-06-06  
**Modular Item System**  
- Pickup manager, chest overhaul, and player refactor  
- Significant item system refactor  

---

## [v0.26.37] - 2025-06-05  
**Asynchronous Chunk System & LOD Integration**  
- Expanded chunk grid from 2x2 to 4x4  
- Each chunk modular with assets and instancing  
- Level of Detail support added: LOD0 (full), LOD1 (reduced), LOD2 (light)  

---

## [v0.25.37] - 2025-06-05  
**Teleportation System**  
- Modular teleporters with two-way linking and visual indicators  
- Editor-friendly, supports area-to-area movement with optional transitions  

---

## [v0.24.37] - 2025-06-05  
**Chunk Manager**  
- Implemented dynamic chunk loading system for scalable world management  

---

## [v0.23.37] - 2025-06-05  
**Destroyables & Resource System Live**  
- Modular DestroyableObject scene with damage detection and animated destruction  
- ResourceObjLife script for HP assignment  
- New Destroyables TileSet and interactive player weapons  

---

## [v0.22.37] - 2025-06-05  
**Patrol System Online**  
- NPC patrol behavior with path following  
- Setup for future PatrolAttack behavior  

---

## [v0.21.36] - 2025-06-04  
**Behavior Follow System (Pets!)**  
- Teleport signals for behavior follow  
- BehaviorFollow script with target finding and teleport logic  

---

## [v0.20.36] - 2025-06-04  
**Modular Scene System & Character Architecture**  
- New Map scene with ScreenGridRef, TileMap, and EnvironmentArea  
- Overworld composition pattern  
- Modular character support with sprite animation and equipment customization  

---

## [v0.19.35] - 2025-06-03  
**World Injector Functional**  
- Fixed title screen launching behavior  
- Updated UI panel assignments  
- World Injector enables real-time weather effects following player  

---

## [v0.18.35] - 2025-06-03  
**Derived Stat System Resolved**  
- All stats update in real time  
- Corrected stale paths in InventoryManager  
- CritDmg formula pending tweaks  

---

## [v0.17.34] - 2025-06-02  
**Stats Interaction System Wiring**  
- Wired core stats with layered dependencies across Vital, Primary, Influence, Tactical, and Rating categories  

---

## [v0.16.31] - 2025-05-30  
**Weather & Environment Effects**  
- Added visual weather FX (rain, snow, clouds, fog, ray lights, falling leaves)  
- Area2D system with EnvironmentArea and EnvironmentShapes for modular effects  

---

## [v0.15.30] - 2025-05-29  
**Equipment System**  
- Equipment modifiers, upgraded item database  
- Equip and unequip gear with 31 test items  

---

## [v0.14.29] - 2025-05-28  
**Stats**  
- Statblock, player HUD, player stats, class database implemented  

---

## Earlier Milestones (Summary)  
- v0.13.13: Item Pickup & Storage system with 64 inventory and storage slots plus equipment  
- v0.12.12: Class system and stat blocks for multiple subclasses  
- v0.11.11: Item framework with interactables like chests and doors  
- v0.10.10: Player inventory foundations  
- v0.09.09: Menu input handling  
- v0.08.08: Signal architecture for UI and movement control  
- v0.07.07: Basic equipment system (slots only)  
- v0.06.06: Named input actions  
- v0.05.05: Basic world scene with physics layers and tilemaps  
- v0.04.04: Dynamic spawning chests  
- v0.03.03: Item databases  
- v0.02.02: Inventory singleton autoload  
- v0.01.01: Player movement and controls  

---
