## 👤 NPC Generator System

The NPC Generator System provides a data-driven pipeline for creating NPC definitions used throughout the game.

NPCs are authored through CSV data and automatically converted into optimized `.tres` resources, allowing large NPC databases to be maintained without manual resource creation.

The system is responsible for:

* NPC resource generation
* NPC identity management
* portrait assignment
* dialogue assignment
* shop assignment
* NPC categorization
* generated NPC indexing

See:

- Dialogue System
- Shop System
- Portrait System

---

### 🧱 NPC Resource Architecture

Each generated NPC is represented by an `NPCData` resource.

NPC resources contain:

* NPC identifier
* display name
* title
* portrait reference
* NPC type
* dialogue reference
* shop reference

NPCs are fully data-driven and do not require manual resource creation.

---

### 🗂️ NPC Organization

NPC resources are automatically organized using category and optional subcategory metadata.

Example:

```text
data/databases/entity/npcs/
├── towns/
│   ├── merchants/
│   ├── blacksmiths/
│   └── innkeepers/
│
├── wilderness/
│   ├── travelers/
│   └── quest_givers/
│
└── bosses/
```

This organization allows NPC databases to scale while remaining easy to navigate and maintain.

---

### 🎭 NPC References

NPC resources act as lightweight data containers that connect multiple gameplay systems.

Supported references include:

* portrait assets
* dialogue definitions
* shop definitions
* NPC classifications

These references allow runtime systems to retrieve NPC-specific data without embedding logic inside the resource itself.

---

### ⚙️ Generation Pipeline

The generation process:

1. Reads NPC CSV definitions
2. Validates NPC data
3. Creates `NPCData` resources
4. Assigns dialogue, portrait, and shop references
5. Routes resources into category folders
6. Saves `.tres` resources
7. Rebuilds the NPC index

---

### 🔎 Generated NPC Index

The NPC Index Generator scans generated NPC resources and builds a runtime lookup index.

The runtime database provides:

* fast NPC loading
* deterministic resource discovery
* filesystem-independent lookups
* scalable NPC organization

The index is automatically rebuilt whenever NPC generation runs.

---

### ✅ System Responsibilities

The NPC Generator System:

✅ Creates NPC resources  
✅ Handles CSV → Resource conversion  
✅ Assigns NPC metadata  
✅ Connects NPCs to dialogue and shops  
✅ Organizes NPC resources  
✅ Builds runtime indexes

The NPC Generator System does **not**:

❌ Control NPC behavior  
❌ Run conversations  
❌ Operate shops  
❌ Manage AI or movement  
❌ Spawn NPCs into maps

Those responsibilities belong to:

- Dialogue System
- Shop System
- NPC Runtime System
- Spawn System
