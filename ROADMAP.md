# Roadmap

> The development roadmap for this project.  
> Features, priorities, and timelines may change as development continues.

---

# Current Status

**Development Phase:** Core Framework Expansion

---

# Completed Systems ✅

<details>
<summary>Engineering Foundation</summary>

- [x] Core project architecture
- [x] Modular system structure
- [x] Autoload manager framework
- [x] Save and load system
- [x] Version control and development workflow
- [x] Localization pipeline
- [x] CSV-driven resource generation
- [x] Database indexing architecture
- [x] Runtime resource databases
- [x] Generator pipeline foundation
- [x] Modular system documentation

</details>

<details>
<summary>Core RPG Framework</summary>

- [x] Player movement framework
- [x] Inventory system foundation
- [x] Equipment framework
- [x] Item database architecture
- [x] Shop system foundation
- [x] Quest framework
- [x] Dialogue framework
- [x] NPC interaction system
- [x] UI management framework
- [x] Event-driven system communication

</details>

<details>
<summary>Character & Progression Systems</summary>

- [x] Character stat framework
- [x] Primary and derived stats
- [x] Resistance systems
- [x] Class data architecture
- [x] Stat formula database foundation
- [x] Experience system foundation
- [x] Difficulty framework foundation
- [x] Effect system foundation

</details>

---

# Active Development 🚧

## Item & Economy Expansion

Goal:
> Complete the item pipeline and expand item support for gameplay systems.

Current focus:

- [ ] Expand inventory capacity for additional item categories
- [ ] Add dedicated crafting material storage
- [ ] Continue item database expansion
- [ ] Integrate remaining item categories into gameplay systems
- [ ] Prepare item systems for future crafting expansion

---

## Foundational System Stabilization

Goal:
> Resolve remaining architectural gaps and establish reliable system ownership.

Current focus:

- [ ] Refactor Level system into Map system
- [ ] Update save/load and map transition workflows
- [ ] Move chunk configuration ownership into MapData
- [ ] Connect MapData → Chunk Manager → Chunk Loader pipeline
- [ ] Resolve remaining system integration issues

---

## Launch & Framework Architecture

Goal:
> Transform launch flow from a centralized controller into a modular orchestration layer.

Current focus:

- [ ] Separate player injection from launch responsibilities
- [ ] Modularize camera injection
- [ ] Modularize weather injection
- [ ] Modularize map injection
- [ ] Improve framework flexibility for multiple genres

---

## Character & Entity Expansion

Goal:
> Create a modular actor framework capable of supporting different game styles.

Current focus:

- [ ] Modularize character visuals and animations
- [ ] Support swappable actor configurations
- [ ] Expand class identity data
- [ ] Evaluate character appearance resource architecture

---

## Remaining System Expansion

Goal:
> Continue expanding existing framework pillars into complete reusable systems.

Current focus:

- [ ] Expand combat framework
- [ ] Expand effects and status systems
- [ ] Expand progression systems
- [ ] Continue creator tooling improvements
- [ ] Expand documentation and examples

---

# Planned Roadmap 📋

## Phase 1 — Foundational Systems

Goal:
> Establish stable foundations and resolve remaining architectural gaps before major feature expansion.

Focus:

- Complete remaining core framework stabilization
- Finalize data-driven architecture
- Improve system ownership and modularity
- Resolve major milestone issues
- Strengthen development workflows and documentation

Major Work:

- Item system expansion
- Inventory expansion for crafting materials
- Map system refactor
- Chunk management ownership refactor
- Launch flow modularization
- Character / Actor modular architecture
- Difficulty and stat integration fixes

---

## Phase 2 — Framework Baseline Completion

Goal:
> Bring existing RPG systems to a reliable baseline suitable for building games.

Focus:

- Complete remaining gameplay foundations
- Expand incomplete system layers
- Improve framework flexibility
- Validate cross-system integration

Systems:

- Combat expansion
- Effects and status systems
- Progression systems
- Economy systems
- Crafting systems
- Quest and dialogue expansion
- World interaction systems

---

## Phase 3 — Playable Demo Slice

Goal:
> Create a complete playable experience demonstrating the capabilities of the framework.

Features:

- Playable region
- Character creation
- Player progression
- Combat loop
- NPC interactions
- Quest flow
- Loot progression
- Inventory and equipment
- Complete gameplay loop

---

## Phase 4 — Game Jam Preparation & Research

Goal:
> Prepare the framework and development process for external testing through game jam development.

Focus:

- Research game jam requirements
- Identify scope limitations
- Create development workflow
- Build reusable templates
- Prepare tutorials and documentation
- Create example projects

---

## Phase 5 — Alpha Expansion & Beta Preparation

Goal:
> Use game jam feedback to improve the framework and prepare for the next development phase.

Focus:

- Analyze gameplay feedback
- Improve framework usability
- Address discovered limitations
- Expand documentation
- Improve creator workflows
- Prepare beta-level development process

# Future Ideas 💡

Potential future additions:

- Multiplayer support
- Modding tools
- Procedural generation systems
- Additional game genres
- Expanded editor support
- Community tools

---

# Development Philosophy

This project is built around:

- Modular architecture
- Data-driven development
- Reusable systems
- Generator-powered workflows
- Documentation-first design
- Scalable RPG framework foundations

---

# Versioning

Versions follow:

```

v[major].[milestone].[day]

```

Example:

```

v0.52.195

```

Development progress is tracked through:

- `CHANGELOG.md` → Completed updates and releases
- `ROADMAP.md` → Future direction and milestones
- Documentation → System architecture and usage
```
