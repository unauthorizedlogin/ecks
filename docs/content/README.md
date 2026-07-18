# 🎮 Content Layer

The **Content Layer** contains the actual game content created using ecks.

## Includes

- Scenes
- Maps
- NPC instances
- Enemies
- Interactions
- World objects
- Game-specific assets

---

## 📁 Folder Organization

Unlike the framework itself, the `content/` directory has **no required structure**.

Developers are free to organize their project however best fits their workflow.

### Example

```text
content/
├── characters/
├── enemies/
├── items/
├── maps/
├── ui/
├── audio/
├── sprites/
├── animations/
└── effects/
```

Or organize by gameplay progression.

```text
content/
├── chapter_01/
├── villages/
├── bosses/
├── dungeons/
└── cinematics/
```

The framework intentionally does not enforce a specific organization.

---

## Content References Data

Content references the **Data Layer** to define behavior.

For example, a scene may contain:

- An NPC using NPC Data
- A quest giver using Quest Definitions
- An enemy using Stat Data
- An interactable object using Interaction Data

The **Content Layer** represents the playable world built on top of ecks.
