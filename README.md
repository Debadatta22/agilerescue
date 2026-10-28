# agilerescue
AI-assisted disaster intelligence &amp; emergency dispatch platform. Connects live USGS, Open-Meteo, &amp; NASA GIBS signals with structured incident intake, deterministic Dijkstra route planning, explainable response plans, human-in-the-loop review, and replanning.


# AgileRescue

<div align="center">

<h3>From live disaster signals to an explainable response plan.</h3>

<p>AI Emergency Dispatch Agent & EarthWatch Live Disaster Intelligence Platform</p>

[[https://img.shields.io/badge/License-MIT-blue.svg?style=flat-square]](LICENSE)
[[https://img.shields.io/badge/Frontend-React%2018%20%7C%20TypeScript%20%7C%20Tailwind-teal.svg?style=flat-square]](https://react.dev/)
[[https://img.shields.io/badge/Maps-Leaflet%20%7C%20CARTO%20%7C%20Esri-emerald.svg?style=flat-square]](https://leafletjs.com/)
[[https://img.shields.io/badge/Backend-Node.js%20%7C%20Express-slate.svg?style=flat-square]](https://expressjs.com/)
[[https://img.shields.io/badge/AI-Gemini%20API%20%7C%20Structured%20Extraction-cyan.svg?style=flat-square]](https://ai.google.dev/)
[[https://img.shields.io/badge/Tests-6%2F6%20Passed-brightgreen.svg?style=flat-square]](#testing)

<p align="center">
  <a href="#key-capabilities">Key Capabilities</a> •
  <a href="#earthwatch-live-intelligence">EarthWatch</a> •
  <a href="#architecture">Architecture</a> •
  <a href="#quick-start">Quick Start</a> •
  <a href="#demonstration-workflow">Demo Flow</a> •
  <a href="#data-sources">Data Sources</a>
</p>

</div>

---

> [!CAUTION]
> **Operational & Safety Notice**: AgileRescue is a prototype decision-support system designed for disaster-response coordination and technical evaluation. Information may be incomplete or delayed. It is not an official emergency service and does not replace instructions from local disaster management authorities. In an actual emergency, contact local emergency services immediately.

---

## 📌 Executive Summary

Emergency coordination during extreme weather and geophysical events requires turning fragmented information—phone transcripts, satellite imagery, public weather models, and official alerts—into an operational dispatch recommendation.

**AgileRescue** bridges two critical workflows:
1. **EarthWatch**: Continuous situational awareness combining live meteorological telemetry, global atmospheric circulation, seismic event streams, and orbital satellite composite overlays.
2. **Operations & AI Dispatch**: Schema-validated incident extraction, deterministic shortest-path route calculation across regional transport graphs, multi-factor resource ranking, human-in-the-loop review, and dynamic replanning when conditions escalate.

---

## 🌟 Key Capabilities

### 🛰️ EarthWatch Live Intelligence
- **Atmospheric Circulation**: Direct integration with [Earth Nullschool](https://earth.nullschool.net/#current/wind/surface/level/orthographic=-277.50,0.00,1169) for orthographic GFS supercomputer forecast models (surface wind, temperature, precipitable water, and 850hPa monsoon vectors).
- **High-Frequency Weather Telemetry**: Live conditions and forecast assimilation from [Open-Meteo](https://open-meteo.com/) for rainfall, wind speed/direction, and relative humidity.
- **Seismic Monitoring**: Real-time M2.5+ earthquake streams from the [USGS Earthquake GeoJSON Feed](https://earthquake.usgs.gov/earthquakes/feed/v1.0/) with hypocenter depth and official catalog links.
- **Satellite & Terrain Layers**: [NASA Earthdata GIBS](https://nasa-gibs.github.io/gibs-api-docs/) WMTS daily composites (MODIS Terra and VIIRS SNPP), [CARTO Voyager](https://carto.com/basemaps/), [Esri World Imagery](https://www.arcgis.com/home/item.html?id=10df2279f9684e4a9f6a7f08febac2a9), and [OpenTopoMap](https://opentopomap.org/) contours.
- **Multi-Agency Alerts**: Live connectors for [GDACS](https://www.gdacs.org/), [Copernicus EMS Rapid Mapping](https://mapping.emergency.copernicus.eu/), [India Meteorological Department (IMD)](https://mausam.imd.gov.in/), and [NASA FIRMS](https://firms.modaps.eosdis.nasa.gov/).
- **Hazard Escalation**: Draft operational incidents directly from external hazard markers with verified provider metadata.

### 📝 Structured Incident Intake
- Multi-modal field report intake via text, simulated radio/voice transcripts, or photo attachments.
- Schema-validated extraction (`parseIncidentReport`) parsing category, location, headcount, reported injuries, access constraints, and missing facts.
- **Preserves Unknowns**: Missing information remains explicitly `null` (never silently converted to `0` or false values).

### 🧭 Deterministic Routing & Resource Matching
- **Regional Dijkstra Graph**: Shortest-path routing over road corridors and navigable waterways across the Majuli–Jorhat network (avoiding misleading straight-line Euclidean vectors).
- **Multi-Factor Scoring**: Transparent weights: Capability Match (40 pts) + Travel Duration/Proximity (35 pts) + Capacity Margin (25 pts).
- **Constraint Enforcement**: Automatically routes around reported road washouts, submerged bridges, and debris corridors.
- **Anti-Duplication**: Enforces responder capacity and availability rules to prevent double-booking.

### 🔄 Human Approval & Dynamic Replanning
- **State Machine Guard**: Strict transitions (`Draft` &rarr; `Awaiting Review` &rarr; `Submitted` &rarr; `Recommendation Ready` &rarr; `Awaiting Human Approval` &rarr; `Approved for Simulation`).
- **Dynamic Replanning**: When incoming field updates arrive (e.g., river levels rise +1.1m, submerging a culvert road and increasing stranded residents from 3 to 6), the engine reroutes assets via safe detours and generates side-by-side **Plan v1 vs. Plan v2** diffs.
- **Audit Logging**: Immutable operational record of automated extractions, provider syncs, plan revisions, and coordinator decisions.

---

## 🏗️ System Architecture

```text
agilerescue/
├── src/
│   ├── app/                # Application bootstrap & route switchers
│   ├── components/         # Reusable UI cards, tables, badges, and modals
│   ├── domain/
│   │   ├── rules/          # State machine transitions (stateMachine.ts)
│   │   ├── schemas/        # Zod & JSON schema validation (incidentSchema.ts)
│   │   └── types/          # Strict TypeScript domain interfaces
│   ├── features/           # 9 complete operational modules (Overview, EarthWatch,
│   │                       # Intake, Command, Live Map, Resources, Plans, Audit, Settings)
│   ├── services/
│   │   ├── providers/      # Typed provider registry (providerRegistry.ts)
│   │   └── routing/        # Deterministic Dijkstra shortest-path engine (dijkstraRouter.ts)
│   └── tests/              # Automated unit and integration test suite
├── server/
│   └── index.ts            # Express server (secure proxy for Gemini, NASA FIRMS, Supabase)
├── supabase/
│   └── migrations/         # 14 relational tables with Row Level Security (RLS)
├── docs/                   # System architecture, data sources, and limitations
├── package.json
├── tailwind.config.js
└── vite.config.ts
