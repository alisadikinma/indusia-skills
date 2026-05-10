# System Architecture Blueprint: Smart Fleet Cargo & Crane Management System (IRN Batam)

This document establishes the high-level system architecture and technical specifications for the Smart Fleet & Crane Management System at IRN Batam. As Lead Systems Architect, my priority is operational resilience in high-interference industrial zones and extreme cost-efficiency.

---

## 1. System Architecture Overview / Ikhtisar Arsitektur Sistem

For fleets under 200 vehicles and associated yard cranes, a **Monolithic Architecture** using **Django + PostgreSQL + Redis** is the only logical choice. 

*   **The Architect’s Opinion:** Attempting a microservices architecture in the Batam industrial corridor is "DevOps suicide." The talent pool in Batam and nearby Singapore-proximate zones is deeply familiar with Python/Django; maintaining a complex Kubernetes cluster for a medium fleet adds unnecessary failure points. A monolith ensures easier handovers and drastically lower maintenance overhead.
*   **Hosting & Compliance:** To comply with **PDP UU 27/2022**, data must reside in Indonesia. We will utilize **Alibaba Cloud Jakarta** or **Biznet Gio**. These provide the lowest latency to Batam via the Singapore-Jakarta fiber backbone.
*   **Communication Protocol:** 
    *   **REST API:** For administrative functions and non-critical data.
    *   **MQTT:** Mandatory for high-frequency sensor streams (GPS/Fuel). MQTT's low-power, small-packet footprint is required for reliable telemetry over shaky 4G/LTE industrial cells.

### Implementation Phases / Fase Implementasi
| Phase | Focus | Key Tech | Est. Deployment | Cost Tier |
| :--- | :--- | :--- | :--- | :--- |
| **MVP** | Core Tracking & Dispatch | Django Monolith, PWA, Basic Fuel | 3 Months | Low |
| **Phase 1** | Automation & Scaling | Flutter, AI Auto-Assign, RFID Gate | 6-8 Months | Medium |
| **Phase 2** | Optimization | CYMS Stacking AI, NLE SP2, Maintenance | 12+ Months | High |

---

## 2. Data Model & Schema / Model Data & Skema

We adopt a **PostgreSQL-first** strategy, utilizing **PostGIS** for all spatial telemetry. 

### Entity Mapping & Relationships
*   **Fleet Core:** `Vehicle` (Asset), `Chassis` (Detachable asset), `Driver`.
*   **Ops & Control:** `Delivery`, `Container_lifecycle`, `Crane_job`.
*   **Compliance & Finance:** `Cert_silo_sio` (Critical for ensuring crane operators hold valid SIO/SILO licenses before starting a job), `Cashbond_ledger` (Tracking driver advances to mitigate financial stress-driven fraud), `Yard_slot`.

### PostGIS Strategy & Performance Targets
To handle millions of GPS pings from 200+ assets, we will implement **Monthly Table Partitioning**. 
*   **Latency Target:** <100ms for spatial geofence queries.
*   **Storage Target:** 5GB per vehicle/year for raw telemetry logs (including fuel/crane sensors).

### SQL DDL Snippet (PostgreSQL)
```sql
-- Vehicle Table with indexing for performance
CREATE TABLE vehicle (
    id SERIAL PRIMARY KEY,
    plate_number VARCHAR(15) UNIQUE NOT NULL,
    current_location GEOGRAPHY(POINT, 4326),
    status VARCHAR(20) DEFAULT 'idle',
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);
CREATE INDEX idx_vehicle_location ON vehicle USING GIST(current_location);

-- Fuel Event for Fraud Detection
CREATE TABLE fuel_event (
    id SERIAL PRIMARY KEY,
    vehicle_id INT REFERENCES vehicle(id),
    fuel_level_percent NUMERIC(5, 2),
    interface_type VARCHAR(10) DEFAULT 'RS-485', -- Recommended for stability
    is_anomaly BOOLEAN DEFAULT FALSE,
    event_timestamp TIMESTAMP WITH TIME ZONE
);
```
*   **Audit Pattern:** Mandatory `deleted_at` (Soft Delete) on all operational tables for historical insurance audits.

---

## 3. Driver Mobile App Architecture / Arsitektur Aplikasi Mobile Driver

For the MVP, we will deploy a **Progressive Web App (PWA)** to bypass App Store friction, transitioning to **Flutter** only when the fleet exceeds 40 drivers.

### Offline-First Strategy (Strategy Per Asset)
The app must function in "dead zones" common in Batam's shipyards.
*   **App Shell:** **Cache-first** with revisioning for instant rendering.
*   **Content/Feeds:** **Stale-while-revalidate** via Service Workers to show last-known jobs while fetching updates.
*   **User Data/ePoD:** **Network-first** with IndexedDB fallback.

### BLE & Persistence
The app architecture includes a background pairing service for the **Escort TD-150 BLE** fuel sensors. This ensures the phone acts as a gateway, preventing "dead zone" data loss by caching sensor pings locally in IndexedDB and flushing via **Background Sync** when connectivity returns.

---

## 4. Dispatcher Console & AI Auto-Assign / Konsol Dispatcher

We will use a **Mapbox-Leaflet hybrid** for the UI. Mapbox GL JS provides vector smoothness for moving assets, while Leaflet manages cost-free standard layers.

### Native AI Auto-Assign
We reject "bolt-on" automation. The system uses a **Native AI engine** embedded in the core database that learns from carrier performance and dwell times.
*   **Ranking Algorithm Logic:** 
    `Score = (0.4 * Proximity) + (0.3 * Driver_SLA_Rating) - (0.2 * Cashbond_Balance) - (0.1 * Fatigue_Index)`
*   **Route Batching:** Clustering logic minimizes empty miles on the Batam-Singapore cargo leg, identifying backhaul opportunities automatically.

---

## 5. Anti-Fraud Tech Stack / Teknologi Anti-Fraud

To eliminate **"Solar Dikencingi"** (the local practice of fuel theft), we utilize a multi-sensor "Triangulation Rule Engine."

### Hardware Selection
1.  **Omnicomm LLS 5:** Recommended for the core fleet. Features **Fuelscan Technology**, which analyzes fuel density to maintain **99.5% accuracy** even with seasonal fuel variations.
2.  **Escort TD-150 BLE:** Recommended for fast deployment on third-party contractor trucks.
3.  **RS-485 Interface:** Mandatory for dual-tank setups (Prime Movers) to ensure signal integrity over distance.

### Triangulation Rule Engine
An anomaly is flagged if:
1.  **Sudden Drop:** Fuel level drops >3% in <2 mins.
2.  **Ignition Status:** Engine is OFF (via Teltonika FMC640).
3.  **Geofencing:** Not in an authorized refuel zone.

---

## 6. RFID UHF Gate & Asset Management / Manajemen Gerbang RFID

Automation is key to preventing manual logbook errors at the terminal gate.
*   **Regulation:** Must comply with **920-925MHz Indonesia** (EPC Gen 2 ISO 18000-6C).
*   **Q-Algorithm:** We implement the **Multi-tag anti-collision Q-algorithm** to prevent "tag-clashing" when a Prime Mover and its attached Chassis (Asset) pass through the gate simultaneously.

### Bill of Materials (BoM) - Per Gate
| Item | Description | Cost (Est. IDR) |
| :--- | :--- | :--- |
| **UHF Reader** | 4-Port Fixed Reader | Rp 8,500,000 |
| **Antennas** | 9dBi Circular Polarized (x2) | Rp 4,000,000 |
| **Controller** | Industrial Gateway | Rp 3,000,000 |
| **Tags** | Chassis Metal-mount (100 pcs) | Rp 2,000,000 |
| **Total** | | **Rp 17,500,000** |

---

## 7. Container Yard Management (CYMS) / Manajemen Yard Kontainer

Efficiency in the yard (Block.Bay.Row.Tier) directly impacts fuel burn for reach stackers.
*   **Retrieval Planner:** The AI optimizes stacking sequences. It ensures that containers with the earliest ETAs are not buried, reducing the "re-stacking" moves that waste fuel and time.
*   **Idle Alert:** Logic triggers an automated alert for any container exceeding 30 days to facilitate immediate demurrage billing and prevent yard congestion.

---

## 8. Mapping & GPS Tracking Stack / Pemetaan & Pelacakan GPS

### Cost Benchmark (50 Vehicles)
| Provider | Strategy | Est. Cost / Month |
| :--- | :--- | :--- |
| **Google Maps** | High accuracy, high price | Rp 5,000,000 - 12,000,000 |
| **Mapbox + OSM** | **Recommended Hybrid** | **Rp 800,000 - 2,500,000** |

### Device Benchmarks
*   **Teltonika FMC640:** Professional grade, 4G. (Est. **Rp 1,600,000** including installation & Batam import tax).
*   **Teltonika FMB920:** Entry-level asset tracking. (Est. **Rp 650,000**).

---

## 9. Integration & AI/ML Layer / Integrasi & Lapisan AI/ML

The system acts as a hub for both internal ops and Indonesian regulatory mandates.
*   **External Portals:** For carriers like **SITC/Infinity** that lack public APIs, we deploy headless browser workers for web scraping/email parsing.
*   **Regulatory API:** 
    *   **NLE INSW (SP2):** Direct integration for "Surat Penyerahan Peti Kemas" to automate gate releases.
    *   **Coretax:** Direct e-faktur generation upon job completion.
*   **AI Feature Set:** Implementation of **OR-Tools solvers** for route optimization and ML models for predictive demurrage based on port congestion data.