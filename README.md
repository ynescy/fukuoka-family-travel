# Fukuoka Family Travel App

[中文版 README →](README.zh.md)

A mobile-first travel companion app built for a real 6-person family trip to Kyushu, Japan (May 2026). Covers day-by-day itinerary, transport, hotels, packing, shopping, and **real-time multi-currency expense splitting** — all in a single HTML file with no build tools.

**[Live Demo →](https://ynescy.github.io/fukuoka-family-travel/)**

---

## Screenshots

<table>
  <tr>
    <td align="center"><img src="docs/screenshot-itinerary.jpeg" width="160"/><br/><sub>📅 Itinerary</sub></td>
    <td align="center"><img src="docs/screenshot-transport.jpeg" width="160"/><br/><sub>🚌 Transport</sub></td>
    <td align="center"><img src="docs/screenshot-hotel.jpeg" width="160"/><br/><sub>🏨 Hotel</sub></td>
    <td align="center"><img src="docs/screenshot-shopping.jpeg" width="160"/><br/><sub>🛍️ Shopping</sub></td>
  </tr>
  <tr>
    <td align="center"><img src="docs/screenshot-split.jpeg" width="160"/><br/><sub>💰 Expense Split</sub></td>
    <td align="center"><img src="docs/screenshot-analysis.jpeg" width="160"/><br/><sub>📊 Analysis</sub></td>
    <td align="center"><img src="docs/screenshot-packing.jpeg" width="160"/><br/><sub>🧳 Packing</sub></td>
    <td></td>
  </tr>
</table>

---

## Features

### 💰 Real-Time Expense Splitting
The core technical feature. Six family members across different devices record expenses in three currencies (JPY / MYR / NTD); the app syncs in real time via Firebase Realtime Database and converts all amounts to MYR for comparison.

- Each expense records who paid, who shares it, and the category
- Settlement panel computes the **minimum number of transactions** to clear all debts (greedy creditor-debtor matching algorithm)
- One-tap "Copy & Send via WhatsApp" to share the settlement summary
- Falls back to `localStorage` when offline; syncs automatically when reconnected

### 📅 Itinerary
Day-by-day timeline (May 11–20) with timestamps, activity descriptions, budget estimates, weather, and Google Maps links for each stop.

### 🚌 Transport
All flights, highway buses, and rental cars in one place — booking codes, departure times, prices, and tips.

### 🏨 Hotel
Hotel cards with check-in/check-out times, address, booking reference, payment status, and map navigation.

### 🧳 Packing
Interactive checklist (24 items) grouped by category with a progress counter.

### 🛍️ Shopping
Curated store guide per location (Tenjin, Hakata) with map links and notes on tax refund eligibility.

### 📊 Spending Analysis
Post-trip breakdown: total spend, per-person share, shared vs. personal costs, and a horizontal bar chart per member.

---

## Tech Stack

| | |
|---|---|
| **Frontend** | Vanilla JS, HTML, CSS — no framework, no build step |
| **Realtime Sync** | Firebase Realtime Database (SDK 9.x compat mode) |
| **Offline** | `localStorage` fallback, transparent to the UI |
| **Layout** | Mobile-first, 390px viewport, CSS custom properties |
| **Hosting** | GitHub Pages |

---

## Architecture

The entire app ships as a single `index.html` (~1,800 lines). This was a deliberate constraint: the file opens directly on any phone without a server, can be shared over WhatsApp, and deploys to any static host.

```
index.html
├── CSS design tokens (--pri, --bg, --txt …)
├── Static data  (itinerary days, hotels, transport, packing, shopping)
├── Firebase init + Realtime Database listener
├── Tab router   (sw(id) — no history API needed)
├── Expense split engine
│   ├── getSplitData() / saveSplitData()  ← Firebase or localStorage
│   ├── toMYR()                           ← currency normalisation
│   ├── calcSettlement()                  ← minimum-transaction algorithm
│   └── renderSplit()                     ← balance cards + settlement UI
└── Per-tab render functions
```

---

## Firebase Security

During the trip the database used open read/write rules so all family members could record expenses immediately. After the trip, rules were tightened to read-only:

```json
{
  "rules": {
    ".read": true,
    ".write": false
  }
}
```

The UI reflects archive mode — write controls are hidden and a notice is shown at the top of the split tab.

---

## Running Locally

Open `index.html` in any browser — no install needed.

To connect your own Firebase project:

```js
const FIREBASE_CFG = {
  apiKey: "...",
  databaseURL: "...",
  projectId: "...",
};
```

Set `TRIP_ARCHIVED = false` to re-enable write controls.
