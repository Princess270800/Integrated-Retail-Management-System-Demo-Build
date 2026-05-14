# Northwind IRMS

**Integrated Retail Management System — Demo Build**

A single-page web application demonstrating an end-to-end retail management solution: barcode scanning, real-time inventory, online ordering, payment processing, customer dashboards, and delivery tracking — all working together in one unified system.

---

## Table of Contents

1. [Overview](#overview)
2. [Features](#features)
3. [Getting Started](#getting-started)
4. [System Architecture](#system-architecture)
5. [User Guide](#user-guide)
6. [Demo Walkthrough](#demo-walkthrough)
7. [Technology Stack](#technology-stack)
8. [Project Structure](#project-structure)
9. [Future Enhancements](#future-enhancements)
10. [Known Limitations](#known-limitations)

---

## Overview

Northwind IRMS is a proof-of-concept retail management platform built to address common operational pain points in small-to-medium retail businesses: disconnected sales channels, manual stock counting, slow checkout, and poor visibility into deliveries.

The solution consolidates point-of-sale, inventory, e-commerce, payments, and logistics into a single integrated interface so that a single action (e.g. a barcode scan or an online order) automatically updates every other module.

This build is a **demonstration prototype** intended to showcase the user experience and core workflows. It is not a commercial-grade release.

---

## Features

### Operations

| Feature | Description |
|---------|-------------|
| **Dashboard** | Live revenue, transaction count, active orders, and low-stock alerts. Includes a 7-day sales trend chart and a real-time activity log. |
| **Barcode Scanning** | Type or click a barcode to register a sale. Stock decrements automatically; unknown SKUs are flagged. |
| **Inventory Management** | Searchable product list with category filters, stock adjustment controls, and an "Add Product" form. Low-stock items are visually flagged. |
| **Order Queue** | Unified view of online and in-store orders with status badges and timestamps. |

### Storefront

| Feature | Description |
|---------|-------------|
| **Online Store** | Customer-facing catalog with 12 products across 5 categories. |
| **Cart & Checkout** | Quantity controls, automatic GST (15%) calculation, free delivery over $40 threshold, three payment methods (card / digital wallet / cash). |
| **Delivery Tracking** | Four-stage progress tracker (Placed → Preparing → In Transit → Delivered) with ETAs and courier assignment. |

### Cross-Module Integration

- A barcode scan reduces stock in the Inventory module **and** logs to the activity feed
- A completed checkout creates a real order visible in both the Orders queue **and** the Delivery tracker
- Stock dropping below 10 units triggers a toast warning across the application
- All modules share a single source of truth — no manual sync required

---

## Getting Started

### Prerequisites

None. The application runs in any modern web browser (Chrome, Firefox, Safari, Edge).

### Running the Demo

1. Download `irms-demo.html`
2. Double-click the file (or right-click → Open With → your preferred browser)
3. The application loads instantly — no server, no install, no configuration

> **Tip:** For the smoothest demo experience, use a Chromium-based browser at 1280×800 or larger.

---

## System Architecture

The demo is implemented as a self-contained single-page application. All seven modules share a common in-memory data layer, which is what enables the cross-module integration described above.

```
┌─────────────────────────────────────────────────────────┐
│                      USER INTERFACE                      │
│  Dashboard │ Scan │ Inventory │ Orders │ Store │ Cart   │
└─────────────────────┬───────────────────────────────────┘
                      │
              ┌───────▼────────┐
              │  Shared State  │
              │  (products,    │
              │   orders,      │
              │   cart, logs)  │
              └───────┬────────┘
                      │
        ┌─────────────┼─────────────┐
        ▼             ▼             ▼
   ┌─────────┐  ┌──────────┐  ┌──────────┐
   │ Render  │  │ Activity │  │  Toast   │
   │ Engine  │  │   Feed   │  │  System  │
   └─────────┘  └──────────┘  └──────────┘
```

In a production deployment, the shared state layer would be replaced with a REST or GraphQL API backed by a relational database (e.g. PostgreSQL), and authentication would be handled by an identity provider.

---

## User Guide

### Navigating

Click any item in the left sidebar to switch modules. The current module is highlighted, and the breadcrumb at the top updates accordingly.

### Scanning a barcode

1. Open the **Barcode Scan** module
2. Either click a quick-code button on the right, or type a barcode into the input field and press Enter
3. The product appears with its current stock level; stock is reduced by 1 and logged to recent scans

Suggested test codes: `8901234567890`, `7350053850149`, `4006381333930`. Try `9999999999999` to see the unknown-SKU error path.

### Adding a product to inventory

1. Open **Inventory**
2. Click **+ Add Product**
3. Fill in name, SKU, category, price, initial stock, and an emoji icon
4. Click **Add Product** — the SKU is registered and appears in the catalog immediately

### Placing an online order

1. Open **Online Store**
2. Click **+ Add** on any in-stock product
3. Open **Cart & Checkout**
4. Adjust quantities, confirm the delivery address, select a payment method
5. Click **Pay Now** — payment processing simulates for ~1.5 seconds, then a new order appears in the Delivery tracker

### Advancing a delivery

In the **Delivery Tracking** module, each order has an **Advance Step** button that moves it through the four stages. Use **Advance All** to demo the full flow quickly.

---

## Demo Walkthrough

A recommended sequence for the screencast submission:

1. **Dashboard** — show live stats, sales chart, activity feed
2. **Barcode Scan** — scan `8901234567890` (Highland Oat Milk)
3. **Inventory** — confirm the stock for Highland Oat Milk has decreased; filter by "Low Stock" to show the threshold logic
4. **Online Store** — add 3 products to cart
5. **Cart & Checkout** — adjust quantities, select payment method, complete the order
6. **Delivery Tracking** — the new order appears at the top; advance it through all four stages
7. **Orders** — show the unified order queue with both online and in-store entries
8. **Dashboard** — return to confirm revenue, transaction count, and activity log have all updated

This flow demonstrates every required feature and shows the integration between modules.

---

## Technology Stack

| Layer | Technology | Reason |
|-------|------------|--------|
| Markup | HTML5 | Universal browser support |
| Styling | CSS3 (custom properties, grid, flexbox) | Zero dependencies, fully themeable |
| Logic | Vanilla JavaScript (ES6+) | No build step, runs anywhere |
| Typography | Fraunces, Inter Tight, JetBrains Mono (Google Fonts) | Distinctive editorial feel |
| Charts | Hand-authored SVG | Lightweight, no charting library required |

The entire application is delivered as a single ~60 KB HTML file with no external dependencies beyond web fonts.

---

## Project Structure

```
irms-demo/
├── irms-demo.html       # The complete application (markup + styles + logic)
└── README.md            # This file
```

For a production version, the recommended structure would split concerns:

```
irms/
├── src/
│   ├── components/      # Reusable UI components
│   ├── modules/         # Dashboard, Scan, Inventory, etc.
│   ├── services/        # API clients, payment gateway
│   ├── state/           # Centralized store
│   └── styles/          # Design tokens, themes
├── server/
│   ├── routes/          # REST endpoints
│   ├── models/          # Database models
│   └── migrations/      # Schema versioning
├── docs/
└── tests/
```

---

## Future Enhancements

The following capabilities are out of scope for this demonstration but would be priorities for a production release:

- **Persistent storage** — connect to a real database so state survives page reloads
- **Real payment integration** — Stripe, Square, or local equivalents (e.g. Worldline, POLi)
- **Authentication & roles** — separate Manager, Staff, and Customer permissions
- **Real barcode hardware** — WebHID integration for USB and Bluetooth scanners
- **Multi-store support** — inventory and sales scoped per branch
- **Reporting & export** — scheduled CSV/PDF reports, accounting software integration
- **Mobile app** — native iOS and Android clients sharing the same backend
- **Audit trail** — immutable log of stock adjustments and refunds for compliance
- **Customer accounts** — order history, saved addresses, loyalty points

---

## Known Limitations

This is a demonstration prototype. The following are intentional simplifications:

- **No persistence** — refreshing the browser resets all data to the initial demo state
- **Payments are simulated** — no real gateway is contacted; the 1.5-second delay is cosmetic
- **No authentication** — the demo opens directly to the manager view
- **Single-user only** — no concurrent editing, no conflict resolution
- **Static seed data** — the product catalog is hard-coded into the HTML file
- **No backend** — all logic runs in the browser

---

## Credits

Built as a Task 3 submission for the ICT project brief on Integrated Retail Management Systems.

Designed and implemented to demonstrate best-practice development processes, planning and implementation tooling, and clear communication of technical findings to project stakeholders.
