# Domain-Specific Rule Repository

**E-Commerce Supply Chain & Order Management Rules**

This repository contains **50 comprehensive domain-specific rules** for a realistic e-commerce platform. These rules are designed for testing the **NomosAI H1 Consistency Experiment** to demonstrate how shared memory reduces divergence in domain-specific business logic.

---

## Why Domain-Specific Rules?

**Generic coding guidelines** (e.g., "Use camelCase for variables") are easily solved by linters and formatters. The **real value** of NomosAI is in **domain-specific business logic** where divergence causes actual production bugs:

- ❌ **Problem:** Alice thinks "ShipmentConfirmed" means package delivered, Bob thinks it means package left warehouse → integration failure
- ✅ **Solution:** Shared memory with canonical event definitions

---

## Rule Categories (50 Total)

### Supply Chain & Logistics (9 rules)
- `supply_chain.shipment_confirmed` - When/how to emit ShipmentConfirmed event
- `supply_chain.delivery_confirmed` - DeliveryConfirmed vs ShipmentConfirmed distinction
- `carrier.sla_tracking` - Carrier performance tracking and SLA breach handling
- `warehouse.picking_strategy` - Picking strategies (single-order, batch, zone)
- `hazmat.shipping_restrictions` - Lithium battery shipping rules and compliance
- `backorder.handling_rules` - Backorder eligibility and customer communication
- `international.customs_declaration` - HS codes, duties, Incoterms
- `stock_alerts.notify_when_available` - Back-in-stock notification triggers
- `age_verification.alcohol_tobacco` - Age verification for restricted products

### Order Management (8 rules)
- `order.fulfillment_states` - Canonical state machine (CREATED → DELIVERED)
- `order.cancellation_policy` - Cancellation windows and automated scenarios
- `order.customer_created` - CustomerCreated vs CustomerRegistered distinction
- `payment.auth_capture_flow` - Authorization vs Capture timing
- `preorder.management_rules` - Pre-order payment and supplier delays
- `bundles.product_bundles` - Fixed bundles, BOGO, mix-and-match
- `returns.return_policy` - Return eligibility, refund calculation, fraud detection
- `subscription.recurring_orders` - Recurring billing, pause/cancel rules

### Inventory & Pricing (6 rules)
- `inventory.reservation_logic` - Soft/hard reservations, overselling prevention
- `pricing.dynamic_pricing` - Tiered discounts, price lock, stacking rules
- `tax.calculation_rules` - Nexus rules, tax-exempt certificates
- `flash_sales.lightning_deals` - Countdown timers, quantity limits
- `catalog.product_lifecycle` - Product state transitions (DRAFT → ACTIVE → ARCHIVED)
- `marketplace.channel_rules` - Multi-channel inventory sync (Amazon, eBay)

### Fraud & Risk (3 rules)
- `fraud.risk_scoring` - Risk score calculation and automated actions
- `chargebacks.dispute_handling` - Chargeback evidence submission and win rates
- `qa.product_quality_checks` - Receiving inspection and supplier scoring

### Customer Experience (10 rules)
- `customer.segmentation_rules` - VIP, Loyal, At-Risk, Lapsed segments
- `notifications.email_triggers` - Event-driven email automation
- `support.sla_response_times` - P1-P4 ticket prioritization
- `cart_recovery.abandonment_emails` - 3-email recovery sequence
- `wishlist.save_for_later` - Price drop alerts, back-in-stock notifications
- `recommendations.personalization` - Collaborative filtering, hybrid ranking
- `social_proof.urgency_signals` - "X people viewing", "Only N left" badges
- `referral.customer_referrals` - "Give $20, Get $20" mechanics
- `loyalty.points_accrual` - Points earning, redemption, expiration
- `reviews.moderation_policy` - Auto-reject profanity, approve constructive criticism

### Compliance & Governance (6 rules)
- `privacy.gdpr_compliance` - Right to access, erasure, data portability
- `seo.metadata_rules` - Title tags, meta descriptions, structured data
- `content.image_guidelines` - Image resolution, white background requirements
- `giftcards.issuance_rules` - Redemption, partial use, fraud prevention
- `b2b.wholesale_ordering` - Net 30 terms, credit limits, tax exemptions
- `affiliate.tracking_commission` - Last-click attribution, tiered commissions

### Advanced Features (8 rules)
- `experimentation.ab_testing` - Traffic allocation, statistical significance
- `search.ranking_algorithm` - Relevance, popularity, availability scoring
- `analytics.kpi_definitions` - Conversion rate, AOV, LTV calculations
- `multi_currency.exchange_rates` - Auto-detection, markup strategy
- `live_shopping.livestream_events` - Flash deals during livestreams
- `voice_commerce.alexa_integration` - Reorder via Alexa, disambiguation
- `mobile_app.exclusive_features` - Barcode scanner, AR try-on, push notifications
- `sustainability.carbon_tracking` - Carbon footprint calculation, offset programs

---

## Seeding Rules into NomosAI

```bash
# Update seeder to point to domain-repo
export TEAMAI_ITEMS_DIR="/Users/ravvnen/Masters/domain-repo/items"

# Run seeder (from NomosAI project root)
dotnet run --project src/NomosAI.Seeder

# Or use curl to seed manually:
for file in /Users/ravvnen/Masters/domain-repo/items/*.yaml; do
  curl -X POST http://localhost:5000/api/v1/seed \\
    -H "Content-Type: application/json" \\
    -d @$file
done
```

---

## Comparison: Generic vs Domain-Specific

| Aspect | Generic Rules | Domain-Specific Rules |
|--------|---------------|----------------------|
| **Example** | "Use camelCase for variables" | "ShipmentConfirmed = carrier scan, NOT customer delivery" |
| **Impact of Divergence** | Low (cosmetic) | **HIGH (production bugs, data corruption)** |
| **Existing Solutions** | Linters, formatters | **None - This is NomosAI's killer use case!** |
| **Test Value** | Weak (not novel) | **Strong (solves real problem)** |

---

## Real-World Example (Why This Matters)

**Acme Corp Bug (Q3 2024):**

- **Dev A:** "CustomerCreated means user created account"
- **Dev B:** "CustomerCreated means guest checkout (no account)"
- **Result:** Marketing emails sent to guest customers → **GDPR violation → €10K fine**

**With NomosAI:**
```
Alice queries: "What does CustomerCreated mean?"
  → [im:order.customer_created@v1]:
     "CustomerCreated = guest checkout (no account).
      CustomerRegistered = account creation with password."

Bob queries: "Can I send marketing emails to CustomerCreated events?"
  → [im:order.customer_created@v1]:
     "NO - CustomerCreated are guest users. Only send order-specific emails."

Result: Both cite same rule → No GDPR violation ✅
```

---

## Next Steps

1. **Create 100+ test prompts** targeting these domain rules
2. **Seed rules** into NomosAI backend
3. **Run H1 experiment** with Alice (llama3.2) vs Bob (qwen2.5)
4. **Measure Jaccard overlap** of cited rule IDs

---

**Last Updated:** 2026-01-19
**Total Rules:** 50
**Avg Rule Length:** 300-500 lines (comprehensive, real-world complexity)
