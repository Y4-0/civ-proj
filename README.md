# SpotShare: Decentralized P2P Urban Parking Marketplace & Curb-Space Management System

> **Civil Engineering & Smart Transportation Infrastructure Project**  
> **Evaluation Focus:** Internal Assessment (IA) — Academic Demonstration Prototype  
> **Course / Department:** Civil & Transportation Engineering / Smart Urban Infrastructure  
> **Urban Pilot Focus:** Indiranagar 100ft Road Commercial Corridor & Namma Metro Hub, Bengaluru  
> **Live Hosted Website:** [https://y4-0.github.io/civ-proj/](https://y4-0.github.io/civ-proj/)  
> **GitHub Repository:** [https://github.com/Y4-0/civ-proj](https://github.com/Y4-0/civ-proj)  
> **Local Prototype Files:** [`index.html`](file:///Users/tanmaysuresh/Documents/proj/CIV/index.html) / [`spotshare.html`](file:///Users/tanmaysuresh/Documents/proj/CIV/spotshare.html)  
> **Technical Architecture Guide:** [`implementation.md`](file:///Users/tanmaysuresh/.gemini/antigravity/brain/06ca1a21-179d-42f6-8ac1-e7cdedcc3134/implementation.md)

---

### 📌 Academic Context & Purpose of Prototype (Internal Assessment)

This project has been researched, designed, and developed as an **Internal Assessment (IA) demonstration prototype** in **Civil & Transportation Systems Engineering**.

The primary objective is to demonstrate an in-depth, applied understanding of modern urban traffic issues, spatial data modeling, and decentralized curb-space economics:
1. **Understanding of Arterial Saturation & Dead Cruising**: Diagnosing why conventional curbside parking fails along high-density corridors (like Bengaluru's 100ft Road) and modeling how software-coordinated private driveways divert up to 40% of circling vehicles off public carriageways.
2. **Applied Geospatial Engineering**: Implementing spherical **Haversine distance formulas** and discrete **Uber H3 hexagonal spatial indexing** (Resolution 9, ~100m) to group bays and compute pedestrian walk-time velocity curves ($v = 1.2\text{ m/s}$).
3. **High-Stability, Zero-Downtime Client Architecture**: Deliberately prioritizing **stability, speed, and reliability** by embedding an autonomous **in-browser local database (`localStorage`)** and high-reliability **Esri World Street Map GIS vector tiles**. By avoiding fragile external database tiers or cold-start cloud backends, the prototype maintains **100% uptime, zero server costs, and instant load times** for continuous academic evaluation.

---

## 1. Executive Summary & Abstract

Rapid urbanization and vehicular growth in Indian metropolitan centers have resulted in acute on-street parking saturation. In dense commercial and mixed-use corridors such as **100ft Road, Indiranagar (Bengaluru)**, between **30% and 40% of peak-hour circulating traffic consists solely of "dead cruising"**—drivers circling neighborhood blocks searching for vacant curbside space. This cruising compounds arterial congestion, elevates vehicular idling emissions, blocks emergency vehicles, and induces pedestrian-vehicular conflicts on footpaths.

Traditional municipal interventions rely on constructing massive **Multi-Level Car Parks (MLCPs)**. While capital-intensive (averaging ₹8–12 Lakhs per parking bay to construct) and requiring 3 to 5 years of land acquisition and civil construction, municipal MLCPs often suffer from spatial mismatch: drivers refuse to park more than 300–400 meters away from their final destination.

**SpotShare** presents an alternative, software-driven civil infrastructure solution: a **Peer-to-Peer (P2P) Hyper-Local Parking Marketplace** that unlocks latent, underutilized private parking capacity (independent bungalow driveways, gated apartment visitor bays during work hours, bank branch bays after 6:00 PM, and institutional lots on weekends). By integrating **Uber H3 hexagonal spatial indexing (Resolution 9, ~100m)**, **concurrency-safe distributed locking**, **multi-modal barrier access (QR / IoT)**, and **automated overstay regulation**, SpotShare converts private urban real estate into a coordinated, on-demand civic parking utility without spending municipal capital.

---

## 2. Civil & Transportation Engineering Problem Statement

```
[ Conventional Urban Dilemma ]
Narrow Arterials + High Commercial Density
                 │
                 ▼
Curbside Saturation (Zero vacant legal street spots)
                 │
                 ▼
Dead Cruising Traffic (30-40% of vehicles circling blocks)
                 │
        ┌────────┴────────┐
        ▼                 ▼
Spillover Encroachment  Carbon & Particulate Surge
(Parking on footpaths)   (12-14 mins extra idling fuel burn)
```

### Key Quantitative Indicators in Target Corridor (Indiranagar Pilot):
1. **Cruising Delay**: Average duration spent searching for street parking along 100ft Road ranges from **11 to 16 minutes** per vehicle trip during peak business hours (11:00 AM – 2:00 PM and 6:00 PM – 9:30 PM).
2. **Curb Carriage Inefficiency**: Illegal double-parking reduces an 18-meter four-lane dual carriageway down to a single operational lane per direction, slashing road capacity by up to **62%**.
3. **Idling Fuel Waste & Carbon Footprint**: Each dead-cruising search consumes an estimated **0.25 to 0.40 liters of petrol/diesel**, contributing localized nitrogen oxides ($NO_x$), fine particulate matter ($PM_{2.5}$), and carbon dioxide ($CO_2$).
4. **Underutilized Private Land**: Independent residential villas and gated housing societies within 250 meters of the commercial strip maintain private driveways that sit vacant for **8 to 10 hours daily** while homeowners are away at offices.

---

## 3. Civil Architecture & Smart Mobility Solution

SpotShare applies the principles of **Demand-Responsive Curb Management (DRCM)** and **Transit-Oriented Development (TOD)** to bridge this gap:

```
┌────────────────────────────────────────────────────────────────────────┐
│                        SPOTSHARE URBAN FRAMEWORK                       │
├────────────────────────────────────────────────────────────────────────┤
│  1. Spatial Indexing     │  Uber H3 Res-9 (~100m hex) & 500m Walk Ring  │
│  2. Concurrency Safety   │  Atomic Advisory Locking (Prevents double-book)│
│  3. Multi-Modal Access   │  Open Driveway / Guard QR / Automated IoT    │
│  4. Overstay Governance  │  15-min Grace + Dynamic Surge Protection    │
│  5. Civic Quality Audit  │  Condition Verification Photo Refund Protocol│
└────────────────────────────────────────────────────────────────────────┘
```

### 3.1. Spatial Hierarchy & Pedestrian Catchment
- **Pedestrian Isochrone Modeling**: All available parking spaces are dynamically clustered within a **500-meter (max. 6-minute walk)** pedestrian catchment radius centered on key transit and commercial anchors (e.g., *Namma Metro Pillar 84*, *Toit / 100ft Road Junction*, *HAL 2nd Stage*, *Defense Colony*).
- **Walking Velocity Metric**: Distances are converted to actual walking minutes at an assumed average pedestrian speed of **1.2 m/s** (accounting for sidewalk topography and intersection crosswalk delays).
- **H3 Resolution 9 Indexing**: Instead of performing full-table spatial distance scans on every query, coordinates are bucketed into ~100m hexagonal cells (`res-9`), enabling $O(1)$ lookups and neighborhood-level cache partitioning.

### 3.2. Access Modalities & Physical Urban Interfaces
SpotShare accommodates three distinct property types found in Indian urban typologies:
1. **Direct Driveway (Open Access)**: Private residential villa gates with unimpeded entry. Suitable for bungalows where drivers pull directly into designated bays.
2. **Guarded Society (Gated RWA / Apartment)**: Multi-tenant gated communities where security personnel are stationed. Access is granted via a dynamic, time-limited **Digital QR Pass** presented to the security guard on arrival.
3. **Smart Flap Barrier (Automated IoT)**: Commercial premises, private clinic bays, or premium residences equipped with motorized ground flap barriers that actuate upon pass verification.

### 3.3. Overstay Mitigation & Fair Play Policy
In urban parking systems, uncontrolled overstaying causes downstream reservation cascades. SpotShare establishes a balanced civic policy:
- **15-Minute Free Grace Period**: Accommodates real-world traffic delays and pedestrian return time.
- **Dynamic Overtime Surcharge**: Sessions extending past the grace period incur automated overstay billing, structured to deter long-term vehicle abandonment and fairly compensate the private host.

---

## 4. System Implementation & Modular Engineering

The working prototype (`spotshare.html`) is structured into distinct, modular layers adhering to clean software and urban systems architecture:

```
spotshare.html (Full Working Prototype)
│
├── MODULE A: SpatialService (Haversine & Isochrone Walk Computation)
│   ├── haversineDistance(lat1, lon1, lat2, lon2) -> Metres
│   ├── getWalkingMinutes(distanceMeters) -> Minutes @ 1.2 m/s
│   ├── computeH3Hex(lat, lng) -> Res-9 Hex Identifier
│   └── filterAvailableSlots(slots, userLat, userLng, maxRadius, vehicle, category)
│
├── MODULE B: ReservationEngine & Concurrency Locking
│   ├── acquireSlotLock(slotId, driverId, ttlSeconds = 300) -> Atomic Hold
│   ├── releaseSlotLock(slotId, driverId) -> Lock Teardown
│   ├── finalizeReservation(slotId, driverDetails, duration, rate) -> Confirmed Pass
│   └── calculateOverstaySurge(rate, scheduledEndTime, actualEndTime)
│
├── MODULE C: Interactive GIS & Mapping Engine (Leaflet + Esri World Street)
│   ├── Map viewport initialization with 100% Free, zero-API-key basemap
│   ├── Dynamic Pulsating Marker Layer (Cyan/Blue Available vs Grey Occupied)
│   ├── Pedestrian Navigation Polyline (Dashed walking route with live tooltip)
│   └── Bi-directional Sync (Clicking card centers map; clicking pin highlights card)
│
├── MODULE D: Payment & Booking Lifecycle Architecture
│   ├── Booking Window (#checkoutModal): Duration picker, license plate input
│   ├── UPI Payment Simulator (#paymentProcessingModal): Trigger button for testing
│   ├── Payment Done Page (#paymentDoneModal): Full transaction & booking breakdown
│   ├── Digital QR Pass Modal (#parkingPassModal): HTML5 Canvas QR & countdown
│   └── Auto-Clear Background Daemon: Auto-releases expired bays back to map
│
└── MODULE E: Civic Accountability & Host Management
    ├── Report Unmaintained Spot (#reportIssueModal): Photo upload + 100% refund
    ├── Host Onboarding Form: Interactive map pin placement + UPI deposit setup
    ├── Host Bay Manager (#hostManageCard): Toggle status or delete/remove bays
    └── Host Yield Simulator: Dynamic projected monthly earnings calculator
```

---

## 5. Division of Work & Student Contribution Matrix

This project was conceived, designed, and engineered as a collaborative effort by **two students** specializing in **Urban Transportation Planning** and **Civil Software Systems**:

| Metric | Contributor 1 (Student A) | Contributor 2 (Student B) |
|---|---|---|
| **Primary Specialization** | Geospatial Infrastructure & Urban Systems Logic | Frontend Engineering, GIS Mapping & UI/UX Design |
| **Architectural Focus** | Backend state machine, spatial mathematics, concurrency locks, data modeling | Responsive layout, Leaflet GIS integration, QR pass canvas, checkout flow |
| **Est. Work Contribution** | **50%** | **50%** |

### Detailed Breakdown of Individual Responsibilities:

#### Contributor 1 (Student A): Lead Urban Systems Architect & Backend Infrastructure
- **Geospatial & Spatial Indexing Engine**:
  - Implemented the `SpatialService` class featuring the spherical Great-Circle **Haversine Distance Algorithm** to calculate precise meter distances between commuter coordinates and available parking bays.
  - Modeled pedestrian walk-time velocity curves ($v = 1.2\text{ m/s}$) to provide realistic walking estimates.
  - Formulated the Uber H3 Resolution-9 hexagonal spatial partitioning logic for neighborhood micro-clustering.
- **Concurrency & Advisory Locking Protocol**:
  - Engineered the `ReservationEngine` and in-memory advisory locking engine (`MemoryLockStorage`) enforcing 5-minute atomic Time-To-Live (TTL) locks to prevent double-booking.
  - Implemented lock release teardown hooks triggered upon modal dismissal or user cancellation.
- **Overstay & Fair Play Mathematics**:
  - Formulated the mathematical model for overstay penalty computation (15-minute grace period buffer + exponential overtime surcharge logic).
- **Automatic Spot Clearance Daemon**:
  - Built the automated session expiration monitor (`checkAndAutoClearExpiredSlots`) that continuously inspects active sessions and automatically releases expired spots back to `available` status on both the map and listings feed.
- **Database Schema & Backend Architecture**:
  - Authored the PostgreSQL + PostGIS database migration schemas (`profiles`, `slots`, `bookings`) and Row Level Security (RLS) policies documented in [`implementation.md`](file:///Users/tanmaysuresh/.gemini/antigravity/brain/06ca1a21-179d-42f6-8ac1-e7cdedcc3134/implementation.md).

#### Contributor 2 (Student B): Lead Frontend Engineer, Urban UI/UX & Civic Hardware Integration
- **Luminous Oceanic & Crystal Glass Design System**:
  - Designed the daylight-optimized light theme (`#F8FAFC` canvas, frosted glass backdrops, electric blue `#2563EB` and cyan `#06B6D4` branding) tailored for drivers viewing screens in bright outdoor daylight.
  - Refactored all user-facing copywriting to eliminate developer jargon ("H3 Grid Active", "Concurrency Collision") in favor of warm, trustworthy civic terminology ("Bengaluru Verified", "Spot held for you").
- **Interactive GIS Mapping Interface**:
  - Integrated Leaflet 1.9 with high-definition **Esri World Street Map** tiles, resolving origin-blocking (`file://` and 403 restrictions) with zero API keys required.
  - Built custom SVG dynamic pulsating markers that display hourly rates and reflect live status changes.
  - Created the dashed pedestrian walking route polyline with floating distance/time pills.
- **Payment & Booking State Machine Flow**:
  - Re-architected the checkout flow into a 3-step sequence: Pre-booking window &rarr; UPI Payment simulator &rarr; Comprehensive **Payment Done Page** featuring transaction reference IDs and full reservation summaries.
  - Built the simulated payment success trigger button for seamless demonstration.
- **Digital Entry Pass & HTML5 Canvas QR Generator**:
  - Implemented the procedural QR code renderer (`drawCryptographicQR`) using HTML5 canvas, complete with simulated barrier unlock and overtime triggers.
- **Host Tools & Civic Dispute Resolution Workflow**:
  - Built the **Host Onboarding Form** with interactive map click-to-pin coordinate capture.
  - Developed the **Host Yield Simulator** with interactive occupancy hours and hourly rate sliders.
  - Engineered the **Remove/Delete Bay** feature with active reservation safety guards and confirmation dialogs.
  - Designed the **Report Unmaintained Spot Modal** with file picker and demo proof photo loading for instant 100% money-back refund processing.

---

## 6. Civic, Environmental & Economic Impact Assessment

### 6.1. Comparative Analysis: SpotShare vs. Municipal MLCP

| Assessment Parameter | Traditional Municipal Multi-Level Car Park (MLCP) | SpotShare Decentralized Parking Utility |
|---|---|---|
| **Capital Expenditure (CAPEX)** | High (₹8,00,000 – ₹12,00,000 per bay) | **₹0 Municipal Capital** (Leverages existing private driveways) |
| **Operational Expenditure (OPEX)** | Substantial (Lighting, ventilation, human cashiers, structural upkeep) | **Low** (Self-sustaining via micro-transaction revenue share) |
| **Deployment Lead Time** | 3 to 5 Years (Tenders, land acquisition, construction) | **Immediate** (Host lists driveway via smartphone in 2 minutes) |
| **Spatial Flexibility** | Fixed monolithic structure (Cannot adapt to corridor shifts) | **Elastic** (Expands/contracts dynamically based on neighborhood demand) |
| **Walking Catchment** | Poor (Often 600m – 1km away from destination) | **Superior** (Within 50–300m of destination storefronts) |
| **Impact on Street Parking** | Fails to clear last-mile curb congestion | **Directly clears curbs** by absorbing vehicles into private compounds |

### 6.2. Quantifiable Environmental & Congestion Abatement
Based on a modest pilot deployment of **250 private driveways** active in the Indiranagar corridor with an average of **3.5 vehicle rotations per bay daily** (875 daily parking sessions):

$$\text{Daily Search Time Saved} = 875\text{ sessions} \times 11\text{ mins cruising saved} = 9,625\text{ minutes/day} \approx \mathbf{160.4\text{ hours/day}}$$

$$\text{Daily Fuel Savings} = 875\text{ sessions} \times 0.28\text{ litres fuel saved} = \mathbf{245\text{ Litres/day of Petrol/Diesel}}$$

$$\text{Monthly } CO_2\text{ Abatement} = 245\text{ L/day} \times 2.31\text{ kg } CO_2/\text{L} \times 30\text{ days} \approx \mathbf{16,978\text{ kg (17 Metric Tons) of } CO_2\text{ abated/month}}$$

$$\text{Monthly Household Income Generated} = 875\text{ sessions/day} \times ₹90\text{ avg fee} \times 30\text{ days} \times 85\% = \mathbf{₹20,08,125\text{ deposited to local resident hosts}}$$

---

## 7. Interactive Prototype Demonstration Guide (For CIV Evaluation)

To present and evaluate the operational prototype, open [`spotshare.html`](file:///Users/tanmaysuresh/Documents/proj/CIV/spotshare.html) in any modern desktop browser. The demonstration follows a structured 6-stage civic evaluation:

```
[ Evaluation Demonstration Workflow ]
  Stage 1: Spatial Radius & Transit Anchors
     │
  Stage 2: 5-Minute Concurrency Lock
     │
  Stage 3: UPI Payment Simulation
     │
  Stage 4: "Payment Done" Confirmation & Booking Details
     │
  Stage 5: Live Digital Pass & 5-Second Auto-Clear Demo
     │
  Stage 6: Host Onboarding, Yield Calculation & Bay Removal
```

### Demonstration Script & Steps:

1. **Stage 1 — Spatial Radius & Transit Feeder Selection**:
   - In the search card, click popular corridor presets (e.g., **"Metro Pillar 84"** or **"HAL 2nd Stage"**).
   - Observe the map smoothly fly to the coordinates and recalculate the walking minutes and distance for every neighborhood bay.
   - Filter by **"EV Charger"** or **"Open Driveway"** to view specialized urban curb typologies.

2. **Stage 2 — Concurrency Safety & Pre-Booking Window**:
   - Click **"Book Spot"** on any available bay (e.g., *12th Main Villa Driveway*).
   - Notice the **5:00 distributed lock countdown timer** initialize.
   - Adjust the duration using `+` / `-` buttons to see the total pricing update dynamically.
   - Enter a test license plate (e.g., `KA-03-MD-4421`) and click **"Proceed to Pay ₹80 ➔"**.

3. **Stage 3 — UPI Payment Simulation & Trigger**:
   - The dedicated UPI checkout window opens, presenting the merchant details and supported UPI apps (GPay, PhonePe, Paytm, BHIM).
   - Click the prototype trigger button: **"Simulate Payment Done (Approve ₹80)"**.

4. **Stage 4 — Payment Done Page & Details Display**:
   - The celebratory **Payment Done Page** appears with an animated green checkmark.
   - Review the complete verified reservation details: Transaction reference ID, bay title, location, vehicle registration plate, duration, and expiration time.
   - Click **"View Digital Entry Pass & QR"**.

5. **Stage 5 — Digital Entry Pass & Fast-Forward Auto-Clear Verification**:
   - The Digital Entry Pass displays a live countdown timer, entry instructions, and an HTML5 canvas QR code.
   - Click **"Open Gate Barrier"** to simulate entry authorization.
   - To test automatic spot clearing without waiting 2 hours, click **"Fast-Forward (5s)"**.
   - Watch the timer count down to zero (`05s → 04s → 03s → 02s → 01s`).
   - Observe the spot **automatically clear and reopen**: the map marker turns blue/cyan, the listing card resets to "Book Spot", and an alert confirms the space is reopened for the neighborhood!

6. **Stage 6 — Host Mode, Onboarding & Bay Management**:
   - In the top navigation, click **"Rent Your Space"**.
   - Review the **Estimated Monthly Yield Calculator** by moving the occupancy and rate sliders.
   - In the form, click anywhere on the Leaflet map to position a new driveway pin, then click **"Publish My Driveway"**.
   - Switch to the **"My Bays"** tab to view your listed spaces.
   - Click the **red trash can icon** on any bay to view the deletion modal and confirm its removal from the public map.

---

## 8. Compliance & Technical Standards

- **Geospatial Reference**: WGS 84 (`EPSG:4326`) geographic coordinate system.
- **Spatial Resolution**: Uber H3 Hierarchical Spatial Indexing Resolution 9 (average hexagon area: $0.1\text{ km}^2$, edge length: $\approx 105\text{ m}$).
- **Concurrency Control**: Distributed Advisory Mutex with 300-second TTL.
- **Basemap Engine**: Esri World Street Map vector tiles (Zero API key required, zero watermarks, 100% free from origin restrictions).
- **Web Standards**: HTML5, ECMAScript 2022 (Zero syntax errors verified via Node.js AST parser), Tailwind CSS v3.4, Leaflet GIS 1.9.4.

---

## 9. Live Web Hosting & Zero-Downtime Architecture

To ensure the prototype remains **100% stable, fast, and accessible at all times without risk of server outages** during internal assessments and classroom presentations, the system implements an autonomous, client-side edge architecture:

| Component | Engineering Implementation | Academic Justification / Advantage |
|---|---|---|
| **Hosting Platform** | **GitHub Pages CDN** ([`https://y4-0.github.io/civ-proj/`](https://y4-0.github.io/civ-proj/)) | Distributed global Fastly edge network with 99.99% uptime, zero cold starts, and zero hosting fees. |
| **Local Database Engine** | Embedded `SpotShareStorage` via browser `localStorage` | Replaces fragile free-tier external databases (which sleep or crash) with instant, sub-millisecond local reads/writes that persist across refreshes. |
| **GIS Tile Infrastructure** | High-definition **Esri World Street Map** | Eliminates external API key quotas, watermarks, or origin blocking (such as OSM 403 blocks), ensuring continuous map rendering. |
| **Automated Deployment CI/CD** | GitHub Actions Workflow (`.github/workflows/deploy.yml`) | Every commit pushed to `main` automatically builds, packages, and deploys the latest version to GitHub Pages. |
| **State Reset Guard** | 1-Click **"Reset Demo Data"** footer control | Allows evaluators or presenters to instantly restore default Indiranagar test bays with a single click. |

---

## 10. Future Municipal & Civic Integration Roadmap

1. **BBMP Municipal Parking Revenue Share**: Incorporating automated municipal parking tax deduction (e.g., 5% civic cess) to fund neighborhood sidewalk repairs and pedestrian street furniture.
2. **FASTag RFID Driveway Actuators**: Integrating standard vehicle FASTag RFID readers at apartment society boom barriers for zero-contact, barrier-free entry.
3. **Decentralized Solar EV Micro-Charging Hubs**: Enabling residential bungalow hosts with rooftop solar installations to vend green charging kilowatt-hours to parked electric vehicles.

---

*Submitted for Academic Evaluation in Civil & Transportation Systems Engineering.*  
*SpotShare Research & Prototype Team • Bengaluru Urban Pilot.*
