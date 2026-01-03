# Recipta as a Web Component

Recipta is implemented as a standalone Web Component to enable:

- Embedding in other apps
- Framework neutrality
- Local sandboxing

## Recipta Web Component API

### 1. Component Definition
```html
<recipta-receiver></recipta-receiver>
```

### 2. Attributes

| Attribute | Type | Description |
|-----------|------|-------------|
| accept | string | ORF versions accepted (e.g. orf-v1) |
| mode | string | local-only (default) |
| auto-store | boolean | Automatically persist receipts |
| allow-network | boolean | Permit receipt retrieval |

#### Example
```html
<recipta-receiver
  accept="orf-v1"
  mode="local-only"
  auto-store
></recipta-receiver>
```

### 3. Supported Input Channels

#### 3.1 Inline ORF
```javascript
recipta.receive(orfObject)
```

#### 3.2 Receipt Claim
```javascript
recipta.receiveClaim(claimObject)
```

#### 3.3 postMessage
```javascript
window.postMessage({
  type: "recipta:receipt",
  payload: orfObject
})
```

### 4. JavaScript API

#### 4.1 Methods
```javascript
receive(orf: ORFReceipt): Promise<ReceiptResult>
receiveClaim(claim: ORFClaim): Promise<ReceiptResult>
list(): Promise<ORFReceipt[]>
get(id: string): Promise<ORFReceipt>
verify(id: string): Promise<VerificationResult>
```

### 5. Events

#### 5.1 Receipt Received
```javascript
recipta.addEventListener("receipt-received", e => {
  console.log(e.detail)
})
```

**Payload:**
```json
{
  "id": "cb17-2026-01-003891",
  "status": "confirmed",
  "total": 4.50,
  "currency": "USD"
}
```

#### 5.2 Receipt Verified
```json
{
  "id": "...",
  "verification": "signature-valid"
}
```

### 6. Security Model

User confirmation required for:

- Network retrieval
- External sharing

**Additional security principles:**
- Receipts are immutable once verified
- No automatic merchant trust

### 7. Online Commerce Compatibility

Recipta works identically for:

- Shopify
- WooCommerce
- Stripe Checkout
- Custom carts

All systems converge on ORF + assertion.

### 8. Summary

**Recipta provides:**
- A universal receipt receiver
- A local-first integration layer
- A bridge between commerce and personal knowledge systems

**ORF provides:**
- Neutral representation
- Privacy-first semantics
- Long-term interoperability

**Tommy provides:**
- Retailer tooling
- Human-centered confirmation
- Deployment flexibility

Together, they form a sovereign receipt ecosystem.