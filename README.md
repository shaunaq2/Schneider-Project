# FreightPower Search 

> **Note:** This repository contains no proprietary code. It documents the design decisions, architecture, and engineering work completed at Schneider National.

---

## Overview

FreightPower is Schneider's owner-operator platform serving **259,000+ monthly active users** across web and mobile. My internship focused on two interconnected problems:

1. The existing freight search results were returned in an arbitrary order, leaving owner-operators to manually scan through loads to find profitable ones
2. The search UI was not optimized for mobile-first usage, where the majority of owner-operators actually book loads

---

## What I Built

### Multi-Criteria Load Ranking Algorithm
Designed and implemented a ranking system that scores and sorts freight loads based on factors including:
- **Pickup date proximity** — surfacing loads that align with a driver's availability window
- **Total rate** — prioritizing higher-paying loads
- **Deadhead distance** — penalizing loads that require significant unpaid miles to reach
- **Origin/destination relevance** — matching loads to a driver's preferred lanes

The algorithm produces a composite score per load, replacing the previous unordered result set with a ranked list tailored to each search context.

### PostGIS Spatial Data Infrastructure
Built geospatial query infrastructure using PostGIS to power distance-based filtering and deadhead calculations. This enabled the ranking algorithm to factor in real road-network proximity rather than straight-line estimates.

### Touring Algorithm
Implemented a routing algorithm that generates optimized trip routes for a selected load, factoring in trucker-specific constraints (HOS regulations, fuel stops, rest requirements) rather than just raw driving directions.

### Load Comparison Feature
Built a side-by-side load comparison tool allowing owner-operators to compare multiple loads across the most decision-relevant dimensions — rate, pickup date, distance, deadhead, and destination — before committing to a booking. Previously, drivers had to context-switch between individual load detail pages to compare options.

### Interactive Trip Map with Live Data Overlays
Built a route map that integrates multiple third-party APIs to give truckers a complete picture of their trip:
- **Rest stops** — plotted along the route via rest stop API
- **Gas stations with live fuel prices** — sourced from a live pricing API, showing current prices at stations along the route
- **Toll locations and costs** — integrated toll data so drivers can factor road costs into profitability decisions

Each overlay (rest stops, gas stations, tolls) is independently toggleable, letting drivers declutter the map based on what they care about for a given trip.

### FreightPower Search UI Redesign
Redesigned the load search interface in React/TypeScript to:
- Display 4–5 loads per screen without requiring excessive scrolling
- Surface the most decision-relevant information (rate, pickup date, distance) at a glance
- Reduce the number of taps required to view full load details and initiate booking
- Match a phone-native layout optimized for drivers using the app on the road

Worked end-to-end from Figma mockups through production component implementation.

---

## Tech Stack

| Layer | Technologies |
|---|---|
| Frontend | React, TypeScript |
| Backend | Python, PostGIS, PostgreSQL |
| APIs | Rest Stop API, Live Fuel Pricing API, Toll Data API |
| Data | Digital click-event analytics, user behavior data |
| Design | Figma |

---

## Key Decisions

**Why a composite score instead of simple sorting?**
Single-dimension sorting (e.g., highest rate first) ignores the real cost structure of freight. A load paying $3,000 with 200 miles of deadhead may be less profitable than a $2,400 load that's 10 miles away. The composite score weights these tradeoffs explicitly.

**Why PostGIS over application-layer distance math?**
Deadhead calculations need to run across thousands of loads per search. Pushing spatial queries into the database layer via PostGIS was significantly more efficient than fetching raw coordinates and computing distances in Python.

**Why prioritize mobile layout?**
Usage data showed the majority of FreightPower sessions occurred on mobile. The previous UI was designed desktop-first, resulting in poor information density and extra scroll depth on smaller screens.

---

## Impact

- Served a platform with **259,000+ monthly active users** (197K web, 62K mobile)
- Replaced arbitrary load ordering with a data-driven ranking system
- Added load comparison, reducing context-switching between individual load pages before booking
- Reduced scroll depth and click actions required to book a load on mobile
- Delivered a live trip map integrating fuel prices, rest stops, and toll data in a single toggleable view
- Delivered a production-ready UI redesign from Figma to deployed components

---

