# ⚙️ System Layer

The **System Layer** contains the core ecks framework code.

It is responsible for:

- Runtime systems
- Gameplay logic
- Managers
- Controllers
- Processing pipelines
- Event handling
- Framework communication

## Examples

- QuestManager
- DialogueManager
- CombatManager
- InventoryManager
- SaveManager
- RewardManager

---

## 📁 Folder Organization

Systems are organized into functional categories using a consistent directory structure.

```text
system/
├── economy/
├── engine/
├── entity/
├── launch/
├── legal/
├── loot/
├── progression/
├── ui/
└── world/
```

Each category contains one or more subsystems.

### Example

```text
system/
└── progression/
    ├── achievements/
    ├── dialogue/
    └── quests/
```

Within each subsystem, code is typically separated into runtime logic and development tools.

```text
system/
└── progression/
    └── quests/
        ├── runtime/
        └── tools/
```

---

### Runtime

Contains gameplay code executed while the game is running.

Examples include:

- Managers
- Databases
- Runtime helpers
- Resource databases
- Gameplay logic

---

### Tools

Contains editor-only utilities used to build framework content.

Examples include:

- Resource generators
- Index generators
- CSV importers
- Validation tools
- Asset processing scripts

---

The **System Layer** determines how the framework works.

Creators generally do not modify this layer when creating content.

Instead, systems are configured through the **Data Layer**.
