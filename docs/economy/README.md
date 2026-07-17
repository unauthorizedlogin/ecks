---

# 💰 Economy System README

The **Economy System** provides the framework's complete commerce and currency architecture.

It is responsible for managing player wealth, merchant interactions, buying and selling, item distribution, and transaction processing through a collection of specialized managers.

Rather than embedding economy logic into NPCs or UI scenes, Ecks centralizes transactions through dedicated authorities, allowing merchants, currencies, and future economy features to expand without changing gameplay systems.

---

## ✨ Current Features

* Multi-currency support
* Centralized `CurrencyManager`
* Data-driven merchant inventories
* `ShopManager` transaction authority
* Configurable `ShopVendor` definitions
* Buyback inventory system
* Vendor-specific pricing modifiers
* Automatic item routing by category
* Quest purchase objective integration
* Event Viewer transaction logging
* Save/load synchronization
* Inspector-driven merchant configuration
* Localization-ready merchant labels and titles

---

## 🪙 Supported Currency Types

The framework currently includes support for:

* 💰 Coins
* 💎 Gems
* 🪙 Tokens

The CurrencyManager is designed to be easily extended with additional currency types as the economy grows.

---

## 🏛️ Architecture

The economy follows Ecks' centralized authority model.

```text
                Player
                   |
                   ↓
             Shop Vendor
                   |
                   ↓
             ShopManager
                   |
      ┌────────────┼────────────┐
      ↓            ↓            ↓
CurrencyManager InventoryManager ItemDatabase
      |            |            |
      └─────── Transaction Pipeline ───────┐
                                           ↓
                     QuestManager  EventManager  SaveManager
```

World vendors define merchant behavior, while the ShopManager executes every transaction.

---

## 📚 Included Documentation

* 📄 [Currency System Documentation](./currency_system.md)
* 📄 [Shop System Documentation](./shop_system.md)
* 📄 [Shop Vendor](./shop_vendor.md)

Additional economy documentation will be added as the framework expands with new currencies, merchant systems, pricing rules, and gameplay features.

---

## ✅ Summary

The Economy System provides a modular, data-driven foundation for commerce within the Ecks RPG Framework. By separating merchant configuration from transaction logic and centralizing currency management, the framework supports scalable economy features while maintaining clean authority boundaries between gameplay systems.
