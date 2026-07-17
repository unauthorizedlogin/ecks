# ⚙️ Engine Systems

The **Engine Systems layer** provides the foundational runtime infrastructure that powers the Ecks Framework.

These systems are responsible for:

- Framework startup and initialization
- Global runtime state management
- Save and persistence systems
- Localization infrastructure
- Scene transitions
- Core engine services

Engine systems operate independently from gameplay content, providing reusable framework-level functionality that supports every project built on Ecks.

---

# 🧠 Engine Architecture

The engine layer provides the foundation that all gameplay systems build upon.

Architecture:

```text
                 Engine Systems

                       |
        --------------------------------
        |              |               |
 BootManager     GameManager     SaveManager
        |
        |
 LocalizationManager
        |
 TransitionManager
