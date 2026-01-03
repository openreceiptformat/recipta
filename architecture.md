# Recipta Architecture & Integration Specification

Local-First Digital Receipts using ORF

## 1. Purpose

Recipta is a local-first receipt receiver, validator, and distributor designed to work with the Open Receipt Format (ORF) across physical and online commerce.

Recipta intentionally:

- Does not process payments
- Does not require cloud services
- Does not depend on POS vendor APIs
- Does interoperate with retailer systems, personal apps, and note-taking tools

Recipta acts as a receipt middleware layer between merchants and personal software.

## 2. Core Design Principles

### 2.1 Local-First

All receipts are stored and processed locally by default

Network access is optional and explicit

### 2.2 Payment-Agnostic

Payment confirmation is external

Receipts may be asserted, confirmed, or verified independently of payment rails

### 2.3 POS-Optional

POS integration is opportunistic, not assumed

Human confirmation is a first-class verification mechanism

### 2.4 Event-Driven

Recipta emits structured events that other apps may subscribe to

Recipta is not a data silo

## 3. System Components

```
┌─────────────┐
│   Merchant  │
│  (Tommy)    │
└──────┬──────┘
       │ QR / NFC / Link
       ▼
┌─────────────┐
│   Recipta   │
│ (Local App) │
└──────┬──────┘
       │ Events
       ▼
┌────────────────────────┐
│ Notes / Finance / PKM  │
│ Apps (Local Only)      │
└────────────────────────┘
```

### 3.1 Tommy the Tapir (Retail Framework)

- Exposes menu / cart UI
- Generates ORF draft receipts
- Confirms receipts (human or system)
- Optionally integrates with payment providers

### 3.2 Recipta (Receipt Receiver)

- Receives ORF payloads or claims
- Validates structure and signatures
- Stores receipts locally
- Emits receipt lifecycle events

### 3.3 Consumer Apps

- Note-taking apps
- Personal finance tools
- Knowledge bases
- Expense trackers

## 4. Receipt Lifecycle

Recipta recognizes three receipt states:

**Draft → Confirmed → Verified**

### 4.1 Draft

Generated before payment or confirmation

Useful for expense planning, audit trails

### 4.2 Confirmed

Explicitly acknowledged by cashier or system

Mirrors paper receipt authority

### 4.3 Verified

Cryptographically signed or cross-checked

Optional, not required

### 4.4 ORF Fields (Proposed)

```json
{
  "receipt_status": "draft | confirmed | verified",
  "receipt_origin": "pos | tommy | ecommerce | manual",
  "confirmation_actor": "cashier | system | customer"
}
```

## 5. POS Vendor Integration Reality

Modern POS systems do not provide reliable, portable receipt APIs.

| Reality | Implication |
|---------|-------------|
| APIs are vendor-locked | ORF cannot depend on them |
| Auth required | Not consumer-accessible |
| Legacy systems exist | Human confirmation required |
| Receipt ≠ payment | Receipt must stand alone |

**Conclusion:**
Recipta and ORF treat the receipt as an assertion, not a database extraction.

## 6. Receipt Transport Mechanisms

### 6.1 QR Codes

- Most universal
- Suitable for inline ORF or receipt claims

### 6.2 NFC

- Tap-based convenience
- Payload size limited
- Typically carries receipt claims

### 6.3 Web Links

- Online commerce
- Progressive enhancement

## 7. Receipt Claim Model

Instead of transmitting full receipts, merchants may transmit receipt claims.

```json
{
  "type": "orf-claim",
  "issuer": "tommy://merchant/coffee-bar-17",
  "receipt_id": "cb17-2026-01-003891",
  "hash": "sha256:abc123...",
  "expires": "2026-01-03T12:15:00Z",
  "retrieval": {
    "method": "https",
    "url": "https://merchant.example/receipts/..."
  }
}
```

Recipta decides:

- Fetch immediately
- Store as unverified
- Ignore retrieval

## 8. Local-Only Exposure Model

Recipta exposes local interfaces only:

**Supported mechanisms**

- Loopback HTTP (127.0.0.1)
- Custom URL scheme (recipta://)
- Web Share Target
- Embedded Web Component messaging

No inbound public ports are required.