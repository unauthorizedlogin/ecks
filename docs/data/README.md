# 📚 Data Layer

The **Data Layer** is the creator-facing configuration layer of ecks.

It contains the resources, templates, and tools used to define how systems behave.

## Includes

- Godot Resource definitions
- Configuration assets
- System settings
- Templates
- Generators
- Database structures
- UI menu templates

## Examples

- Quest Definitions
- Objective Definitions
- Dialogue Resources
- NPC Data
- Item Definitions
- Stat Definitions
- Reward Definitions
- UI Configuration Resources

---

## 📁 Folder Organization

The structure of the `data/` directory is **intentionally enforced**.

The framework's resource generators, index generators, and runtime database loaders expect resources to exist in specific locations.

Maintaining this directory structure allows content to be discovered, indexed, and loaded automatically during the build process and boot sequence.

Moving or reorganizing these directories will prevent resources from being generated or discovered correctly.

```text
data/
├── databases/
├── generators/
└── templates/
    └── launch/
```

---

The **Data Layer** acts as the bridge between framework code and authored content.

Creators use the Data Layer to configure systems without editing the underlying implementation.
