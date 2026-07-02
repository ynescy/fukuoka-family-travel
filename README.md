# Fukuoka Family Travel App

A mobile-first travel companion app built for a real 6-person family trip to Kyushu, Japan (May 2026). Covers itinerary, transport, hotels, packing, shopping, and real-time expense splitting — all in a single HTML file with no build tools.

**[Live Demo →](https://ynescy.github.io/fukuoka-family-travel/)**

---

## Features

| Tab | Description |
|-----|-------------|
| 📅 Itinerary | Day-by-day timeline with time, location, map links, and weather |
| 🚌 Transport | Shinkansen / bus / ferry booking details and tips |
| 🏨 Hotel | Hotel cards with check-in info and map links |
| 🧳 Packing | Checklist with category filters |
| 🛍️ Shopping | Must-buy items per location |
| 💰 Expense Split | Real-time multi-person expense tracker (Firebase) |
| 📊 Analysis | Spending breakdown by category and person |

The expense split module syncs across all family members' phones in real time via Firebase Realtime Database, with automatic fallback to `localStorage` when offline.

---

## Screenshots

<table>
  <tr>
    <td align="center"><img src="docs/screenshot-itinerary.jpeg" width="180"/><br/><sub>📅 Itinerary</sub></td>
    <td align="center"><img src="docs/screenshot-transport.jpeg" width="180"/><br/><sub>🚌 Transport</sub></td>
    <td align="center"><img src="docs/screenshot-hotel.jpeg" width="180"/><br/><sub>🏨 Hotel</sub></td>
    <td align="center"><img src="docs/screenshot-packing.jpeg" width="180"/><br/><sub>🧳 Packing</sub></td>
  </tr>
</table>

---

## Tech Stack

- **Vanilla JS + HTML/CSS** — no framework, no build step
- **Firebase Realtime Database** — live expense sync across 6 devices
- **localStorage fallback** — works offline; syncs when back online
- **Mobile-first layout** — fixed 480px max-width, touch-optimized

---

## Architecture

The entire app ships as a single `index.html` (~1,800 lines). This was an intentional constraint: the file can be opened directly on any device with no server, sent over WhatsApp, or hosted anywhere.

```
index.html
├── CSS custom properties (design tokens)
├── Static data (itinerary, hotels, transport, packing, shopping)
├── Firebase config + initialization
├── Tab-based SPA router (no history API)
├── Expense split engine
│   ├── getSplitData() / saveSplitData() — read/write with Firebase or localStorage
│   ├── renderSplit() — member balance cards + settlement suggestions
│   └── calcSettlement() — minimum-transaction debt resolution algorithm
└── Per-tab render functions
```

**Expense settlement algorithm:** Given N people's net balances, the app finds the minimum number of transactions to settle all debts using a greedy creditor-debtor matching approach.

---

## Firebase Security

During the trip, the database used test-mode rules (open read/write for family use). After the trip ended, rules were updated to read-only to archive the data:

```json
{
  "rules": {
    ".read": true,
    ".write": false
  }
}
```

The UI reflects this — the add/edit/delete buttons are hidden and an archive notice is shown.

---

## Running Locally

No install needed. Just open `index.html` in a browser.

To connect your own Firebase project, replace the config in `index.html`:

```js
const FIREBASE_CFG = {
  apiKey: "...",
  databaseURL: "...",
  projectId: "...",
  // ...
};
```

Set `TRIP_ARCHIVED = false` to re-enable write controls.
