# 🎭 Dialogue System

The Dialogue System provides the centralized framework for NPC conversations and dialogue-driven interactions.

The system provides:

* NPC dialogue interaction
* Dialogue state resolution
* Dialogue progression
* Flavor dialogue
* Quest dialogue
* Quest offer and turn-in interactions
* Shop dialogue integration
* Dynamic dialogue options
* Dialogue UI integration

---

# 🧠 Dialogue Architecture

```text
                    Dialogue System

                           |
                    DialogueManager
                           |
          ┌────────────────┼────────────────┐
          ↓                ↓                ↓
   DialogueRuntime   Quest Integration   Shop Integration
          |                |
          ↓                ↓
     Dialogue UI       QuestManager
```

`DialogueManager` coordinates NPC interaction and routes dialogue actions.

`DialogueRuntime` owns active conversation state and dialogue progression.

Quest and Shop systems remain responsible for their own gameplay state.

---

# 🔗 Dialogue System Documentation

| SystemPurposeDocumentation |                                                   |                        |
| -------------------------- | ------------------------------------------------- | ---------------------- |
| 🎭 Dialogue Manager        | Central NPC interaction and dialogue controller   | Dialogue Manager       |
| 🎬 Dialogue Runtime        | Active conversation state and line progression    | Dialogue Runtime       |
| 🎯 Quest Offer Controller  | Temporary quest offer state during dialogue       | Quest Offer Controller |
| 🔌 Quest UI Adapter        | Quest state translation for dialogue presentation | Quest UI Adapter       |
| 🎭 Dialogue Resource       | Data-driven dialogue definitions and states       | Dialogue Resource      |

---

# 🔗 System Integration

The Dialogue System integrates with:

* 👤 NPC System
* 📜 Quest System
* 🏪 Shop System
* 🖥️ UI System
* 💾 Save System

Dialogue acts as the interaction layer between NPCs and gameplay systems without owning their underlying gameplay state.

---

# 📌 Design Rule

**`DialogueManager` coordinates interaction.**

**`DialogueRuntime` manages active conversations.**

**Dialogue Resources provide dialogue content.**

The Dialogue System should remain data-driven and act as the interaction authority while quest, shop, NPC, and other gameplay systems retain ownership of their respective state and behavior.
