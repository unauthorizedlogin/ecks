# 💰 Economy System

## Overview

The **Economy System** provides the framework's commerce and currency architecture.

It is responsible for managing player wealth, merchant interactions, buying and selling, item distribution, and transaction processing through specialized economy systems.

The system follows a centralized authority model, separating:

- Currency management
- Merchant configuration
- Shop transactions
- Item routing
- Economy expansion systems

Rather than embedding economy logic into NPCs or UI scenes, ecks centralizes transactions through dedicated managers, allowing currencies, vendors, and future economy features to expand independently.

---

# 🧠 System Architecture

The Economy System is organized around centralized transaction authorities.

```text
                Player
                   |
                   ↓
             Shop Vendor
                   |
                   ↓
             Shop Manager
                   |
      ┌────────────┼────────────┐
      ↓            ↓            ↓

Currency Manager Inventory Manager Item Database

      |
      ↓

Transaction Pipeline

      |
      ├───────────────┬───────────────┐
      ↓               ↓               ↓

Quest Manager   Event Manager   Save Manager
````

World vendors define merchant behavior, while economy managers control transactions and persistence.

---

# 🔗 Economy System Documentation

The following systems make up the Economy layer.

| System             | Purpose                                                               | Documentation                           |
| ------------------ | --------------------------------------------------------------------- | --------------------------------------- |
| 🪙 Currency System | Player currency management, balances, and economy values              | [Currency System](./currency_system.md) |
| 🏪 Shop System     | Merchant transactions, inventories, pricing, and buying/selling logic | [Shop System](./shop_system.md)         |
| 🧍 Shop Vendor     | Merchant definitions, configurations, and vendor behavior data        | [Shop Vendor](./shop_vendor.md)         |

---

# Summary

The Economy System provides a modular, data-driven foundation for commerce within the ecks Framework.

By separating merchant configuration from transaction processing and centralizing currency management, the framework supports scalable economy features while maintaining clear authority boundaries between gameplay systems.
