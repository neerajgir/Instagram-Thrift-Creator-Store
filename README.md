# Instagram Thrift Store — Database Design (ER Diagram)

A database schema designed for a small creator-run store that sells **thrifted fashion items** and **handmade products** through Instagram DMs and WhatsApp.

---

## Project Overview

This project is a database design task. The goal was to model a real-world small business that:

- Receives orders through Instagram DMs and WhatsApp
- Sells two types of products — unique thrifted pieces and handmade items (available in multiple units)
- Needs to track customers, orders, payments, and shipping

No SQL queries, frontend, or backend were built. This submission covers **database design only**.

---

## File Structure

```
/
├── instagram_thrift_store_erd.html   # Full interactive ER diagram (open in browser)
└── README.md                         # This file
```

---

## How to View the Diagram

1. Download `instagram_thrift_store_erd.html`
2. Open it in any modern browser (Chrome, Firefox, Edge)
3. The diagram renders fully in the browser — no setup or installation needed
4. Requires an internet connection on first load (to fetch Mermaid.js from CDN)

---

## Entities

| Entity | Description |
|---|---|
| `CUSTOMER` | Stores customer contact info including Instagram handle and WhatsApp number |
| `ORDER` | One order per customer session; tracks status and total amount |
| `ORDER_ITEM` | Junction table linking orders to products (many-to-many) |
| `PRODUCT` | Core product record shared by both thrifted and handmade items |
| `THRIFT_DETAIL` | Extra fields specific to thrifted items — condition, brand, fabric |
| `HANDMADE_DETAIL` | Extra fields specific to handmade items — material, batch number, made-to-order flag |
| `PAYMENT` | Payment method, status, and transaction reference for an order |
| `SHIPPING` | Delivery address, courier, tracking number, and delivery status |

---

## Relationships

| Relationship | Cardinality | Notes |
|---|---|---|
| CUSTOMER → ORDER | One to many | A customer can place multiple orders |
| ORDER → ORDER_ITEM | One to one-or-more | Every order has at least one item |
| PRODUCT → ORDER_ITEM | One to zero-or-more | A product can appear in many orders |
| PRODUCT → THRIFT_DETAIL | One to zero-or-one | Only thrifted products have this record |
| PRODUCT → HANDMADE_DETAIL | One to zero-or-one | Only handmade products have this record |
| ORDER → PAYMENT | One to zero-or-one | Payment may not exist yet at order creation |
| ORDER → SHIPPING | One to zero-or-one | Shipping may not exist yet at order creation |

---

## Key Design Decisions

### Thrift and handmade products are modelled separately

A single `PRODUCT` table holds common fields (title, price, category, image). Product-type-specific details live in two separate tables:

- `THRIFT_DETAIL` — holds `condition`, `brand`, `fabric`, and `stock_qty` (usually 1, since thrift items are unique pieces)
- `HANDMADE_DETAIL` — holds `material`, `batch_number`, `made_to_order` flag, and `stock_qty` (can be more than 1)

This avoids null-heavy columns and keeps the schema clean and normalized.

### ORDER_ITEM as the junction table

One order can contain multiple products, and one product can appear across multiple orders — a classic many-to-many. `ORDER_ITEM` resolves this and also stores `unit_price` at the time of purchase, so historical order data stays accurate even if product prices change later.

### Payment and shipping are separate entities

Both are linked to `ORDER` with a zero-or-one relationship. This reflects real business flow — an order exists before it is paid or shipped. Keeping them separate also avoids bloating the `ORDER` table with columns that may not apply yet.

### Customer stores both Instagram and WhatsApp

Since this business started on Instagram DMs and WhatsApp, both channels are stored on the `CUSTOMER` entity. `phone` and `email` are marked as unique keys to prevent duplicate registrations.

---

## Evaluation Coverage

| Criteria | How it is addressed |
|---|---|
| Business understanding | Thrift and handmade products modelled with separate detail tables |
| Entity identification | All 8 core entities present — customer, order, order item, product, thrift detail, handmade detail, payment, shipping |
| Relationships and cardinality | All one-to-many and many-to-many relationships defined; junction table used |
| Attribute quality | Attributes are realistic, separated by concern, and named clearly |
| Primary and foreign keys | Every entity has a PK; all FKs marked and placed on the correct side |
| Clarity | Rendered as an interactive HTML diagram; legend and notes included |

---

## Tools Used

- **Mermaid.js** — for rendering the ER diagram (`erDiagram` syntax)
- **HTML/CSS** — for the standalone diagram file
- **Draw target** — browser-renderable, no install required
