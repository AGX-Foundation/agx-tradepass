**AGX TradePass™**
**Data Model (v0.1) - Public Documentation**

---

### Overview

AGX TradePass™ is a scannable, field-verifiable identity layer built on top of the AGX Compliance Registry. It allows buyers, customs officers, vault operators, and verifiers to instantly confirm the status and legitimacy of certified actors across the gold supply chain. TradePass profiles are generated per certified actor-role and are linked to QR-based mobile verification and public-facing actor profiles.

This document defines the standalone data model for the TradePass credential system and associated scan logs.

---

## ✅ Conformance Requirements

Implementations that use AGX TradePass™ **MUST** conform to the structure and behavior outlined in this specification. Specifically:

* All fields in `tradepass_profiles` marked as required MUST be supported.
* Implementations MUST generate or verify the `signature_hash` for each profile issued.
* Systems MUST restrict access to permissioned fields and scan logs unless authorized.
* Scan log data SHOULD be retained for a minimum of 12 months for traceability.

## 🧩 Extending TradePass

Developers MAY extend TradePass profiles with additional metadata fields (e.g., biometric hashes, issuance agency, or links to documents) using optional columns or JSONB structures. Extensions SHOULD maintain backward compatibility and MUST NOT override core profile fields or signatures unless re-signed.

TradePass MAY also be localized for additional countries by including region-specific schemas. Extensions SHOULD be documented separately in line with the base version.

## 🌐 Interoperability

AGX TradePass™ is designed for compatibility with:

* Government licensing systems
* Customs clearance APIs
* Vault and refinery check-in systems
* External KYC/AML platforms

All TradePass identifiers are UUIDv4 by default and MAY be mapped to national systems using alias tables. Profiles are queryable via mobile tools, web APIs, and low-bandwidth field applications.

---

## 📘 1. TradePass Profile Table

### Field Definitions

* `risk_score`: A 0–100 numerical score that reflects the actor’s compliance health. It may be calculated from audit outcomes, frequency, region, and role type.
* `qr_code_url`: A hosted, signed QR image pointing to the profile badge. Used in customs, buyer apps, and export terminals.
* `valid_until`: The expiration date of the actor’s most recent certification for the given role.
* `certification_status`: A status flag that governs whether the actor is currently certified, revoked, or expired. This status gates access to sensitive systems.
* `region`: The area (country or district) where the actor primarily operates. Helps border, customs, and vault agents assess jurisdictional fit.
* `role_type`: Specifies the function the actor is certified to perform (e.g., exporter, field agent). Used to determine eligibility and permissions in other AGX tools.
* `signature_hash`: A tamper-proof digital signature of the profile payload (recommended). Used to validate authenticity of QR scans.
* `status`: Optional metadata for versioning or revocation (`active`, `suspended`, `deprecated`).
* `version`: Tracks which schema version the TradePass profile adheres to.

### `tradepass_profiles`

**What is a TradePass Profile?**
Each TradePass profile represents a verifiable snapshot of a certified actor’s public identity and compliance status within the AGX system. This profile is created whenever an actor’s role is certified and is refreshed with every audit or certification update. It provides a field-safe, scannable, and tamper-resistant way for third parties—such as customs officers, vault operators, or buyers—to instantly confirm the legitimacy of a supply chain actor.

A live representation of each certified actor-role pairing, packaged for public or field-safe viewing.

| Column                 | Type      | Description                                                   |
| ---------------------- | --------- | ------------------------------------------------------------- |
| `id`                   | UUID      | Unique identifier for the TradePass profile                   |
| `actor_id`             | UUID      | FK → `actors.id`                                              |
| `role_type`            | TEXT      | Role being certified (e.g., exporter, trader, assayer)        |
| `legal_name`           | TEXT      | Public-facing name of the actor                               |
| `region`               | TEXT      | Country, district, or operational territory                   |
| `certification_status` | TEXT      | verified, revoked, expired                                    |
| `valid_until`          | DATE      | Certification expiry date                                     |
| `risk_score`           | INTEGER   | Compliance-derived risk rating (0–100)                        |
| `issued_at`            | TIMESTAMP | Timestamp of profile issuance or refresh                      |
| `qr_code_url`          | TEXT      | URL pointing to the TradePass badge image or QR asset         |
| `signature_hash`       | TEXT      | Cryptographic hash of encoded TradePass data for verification |
| `version`              | TEXT      | Data model or badge format version                            |
| `status`               | TEXT      | Profile lifecycle status: active, deprecated, suspended       |

---

## 📗 2. TradePass Scan Logs

### `tradepass_scan_log`

**What is a TradePass Scan Log?**
Scan logs track every instance where a TradePass QR code or credential is scanned by an external system, tool, or agent. These logs provide an audit trail that supports transparency and traceability. They help AGX monitor usage patterns, detect anomalies, and ensure that profile verifications are occurring within the right contexts—especially at sensitive points like export, vault entry, or border transit.

Audit logs capturing when and how a TradePass profile was scanned.

| Column           | Type      | Description                                    |
| ---------------- | --------- | ---------------------------------------------- |
| `id`             | UUID      | Unique identifier for each scan event          |
| `tradepass_id`   | UUID      | FK → `tradepass_profiles.id`                   |
| `scan_method`    | TEXT      | mobile / API / customs / terminal / field-tool |
| `scan_timestamp` | TIMESTAMP | When the scan occurred                         |
| `scan_location`  | TEXT      | GPS coordinates or named location              |
| `scanned_by`     | TEXT      | Role or system performing the scan             |
| `result`         | TEXT      | success / expired / revoked / not-found        |

---

## 🔒 Privacy, Field Safety & Permissions

### Data Privacy

* Personally identifiable information (PII) is minimized and excluded from public TradePass outputs unless explicitly authorized.
* Profile data is signed and optionally encrypted when accessed via mobile field tools.
* Profile lifecycle states (e.g., revoked, expired) are visible to authorized parties only.

### Geographic Context & Localization

* TradePass is designed to operate across jurisdictions and languages, starting with Ghana and Guinea.
* Field interfaces support English, French, Twi, and Malinké with language fallback logic.
* Local regions are geotagged using national district codes to enable customs and regulatory compliance.

### Low-Latency, Low-Connectivity Design

* TradePass badges and scan results are cacheable on-device for offline verification.
* QR payloads are designed to be lightweight and verifiable without internet access.
* Profile refresh occurs automatically when network is restored, with fallback timestamps.

### Permissions

* All profile scans default to a field-safe view unless the scanner is authenticated.

* Admins and verifiers with token access may unlock expanded compliance views (e.g., audit history).

* Scan logs are retained in compliance with AGX data retention policy and are auditable.

* TradePass exposes only public and permissioned compliance fields.

* QR scans trigger access to a minimal, signed profile by default.

* Role-based access to full audit logs or certification documents must be secured by tokenized or authenticated sessions.

## 🔗 Integration Points

* Pulls source data from: `actors`, `certifications`, `audits`
* Used by: mobile field tools, customs agents, TradePass scanners
* Works offline with sync-capable tools in low-connectivity environments

---

**License:** Open Documentation (CC BY-SA 4.0)
**Repository:** [https://github.com/agx-foundation/agx.network-registry](https://github.com/agx-foundation/agx.network-registry)
