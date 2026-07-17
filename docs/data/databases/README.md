# 💬 Dialogue Database Architecture

## Overview

The Databases provides centralized runtime access to dialogue resources through the engine's standard database architecture.

Like all framework databases, it uses a three-layer pipeline:

1. **Autoload Database Manager**
   - Runtime-facing API
   - Handles initialization
   - Provides gameplay access

2. **Registry Scanner / Resource Database**
   - Loads indexed resources
   - Validates entries
   - Maintains runtime lookup maps

3. **Generated Index File**
   - Created automatically by the generator system
   - Provides deterministic resource paths
   - Removes runtime filesystem scanning

This architecture provides fast, scalable resource access while maintaining a creator-friendly workflow.

---

# 🏗️ Architecture

```text
Database System
│
├── Autoload Database Manager
│
├── Registry Scanner
│
└── Generated Index
````

---

# 🚀 Database Manager

## al_*_database.gd

The database autoload is the primary gameplay access layer.

Responsibilities:

* Initialize database resources
* Load registry data
* Provide lookup functions
* Expose validated resources to gameplay systems

---

# 📚 Registry Layer

## *ResourceDatabase

The registry manages loaded resources after initialization.

Responsibilities:

* Resource registration
* ID validation
* Duplicate detection
* Runtime lookup storage

Resources are stored using dictionary-based lookup for fast access.

Example:

```text
Resource ID
      |
      ↓
Resource Instance
```

---

# 🏭 Generated Index

## *_index.gd

The index file is automatically generated.

It provides:

* Resource paths
* Export-safe references
* Deterministic loading

Creators never manually edit generated index files.

---

# 🔄 Loading Pipeline

```text
Resource Created
        |
        ↓
Generator Scanner
        |
        ↓
Generated Index
        |
        ↓
Registry Database
        |
        ↓
Runtime Lookup
```

---

# 🛠️ Creator Workflow

Creating database content:

1. Create resource
2. Assign unique ID
3. Place resource in database folder
4. Run generator

The framework automatically:

✅ Detects resources
✅ Generates index entries
✅ Loads database entries
✅ Makes resources available at runtime

---

# 🔗 System Integration

The Dialogue Database provides resources for:

* NPC systems
* Quest systems
* Dialogue runtime
* Localization
* Event systems

---

# Summary

The Dialogue Database provides a scalable resource management layer built around generated indexing, registry-based loading, and runtime-safe access.

The same database architecture is shared across the framework, allowing every content system to scale without manual registration or runtime filesystem scanning.

---

# 💬 Database Contents

The Dialogue Database manages all dialogue-related resources used throughout the framework.

Supported dialogue content includes:

- Quest dialogue
- Flavor dialogue
- NPC conversations
- Story interactions
- Optional world dialogue

Resources are organized by unique dialogue identifiers and exposed through the database API.

Example:

```text
Dialogue ID
      |
      ↓
Dialogue Resource
      |
      ↓
Dialogue Runtime
```

That keeps every database README consistent and avoids having 15 copies of the same architecture explanation.
```
