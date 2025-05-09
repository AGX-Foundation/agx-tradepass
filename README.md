# 🪪 AGX TradePass™

**AGX TradePass™** is a portable digital identity credential issued to verified actors in the AGX Registry. It enables real-time, QR-based verification at checkpoints such as customs, refineries, vaults, and buyer onboarding interfaces.

---

## 🔍 Key Features

- Verifiable public profile for any certified actor
- Unique QR code tied to actor ID and role
- Public and permissioned views (field-safe and admin-rich)
- Compatible with mobile and low-bandwidth environments
- Links to audit logs, certifications, and role metadata

---

## 🧩 TradePass Profile Fields

| Field                 | Description                                           |
|----------------------|-------------------------------------------------------|
| `actor_id`           | Unique identifier from the registry                   |
| `legal_name`         | Official or trade name of the entity                  |
| `role_type`          | Certified role (e.g., exporter, assayer, field agent) |
| `certification_status` | Current role certification status                  |
| `valid_until`        | Expiry date of current certification                  |
| `region`             | Active or assigned area of operation                  |
| `risk_score`         | Compliance-derived trust signal (0–100)              |

---

## 📱 Example Use Cases

- **Customs & Border Agents:** Scan QR to confirm exporter status
- **Buyers/Refiners:** Confirm identity and certification of sellers
- **Vault Operators:** Link TradePass ID to incoming parcels
- **Field Agents:** View actor history during site inspections

---

## 🔐 Endpoints (Planned)

- `GET /tradepass/:actor_id` — returns public profile
- `GET /tradepass/scan/:qr` — used in field-facing mobile tools

---

## 🖼️ QR Generator

All TradePass credentials are issued with a dynamic, scannable QR code that encodes:
- Actor ID
- Role
- Signature hash
- Expiry timestamp

Use the `/qr-generator/` module to generate and print badges for field use.

---

## 🔗 Integration Points

- `/actors/`, `/certifications/`, `/audits/` – profile data source
- `/mobile/` – sync-enabled interface for field agents and customs staff
- `TradePass API` – standalone or embedded in AGX Vault, Trade, or Compliance tools

---

© 2025 AGX Foundation. Licensed under CC-BY-SA 4.0
