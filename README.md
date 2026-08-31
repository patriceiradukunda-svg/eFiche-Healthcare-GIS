# eFICHE HEALTHCARE MARKET INTELLIGENCE SYSTEM - QGIS Project

## Overview

This is a professional QGIS project (`.qgz`) containing a comprehensive healthcare facility database for Rwanda, integrating eFiche client facilities with prospect facilities identified through OpenStreetMap.

The system enables:
- Visualization of healthcare facility distribution across Rwanda
- Client vs. Prospect facility identification and market coverage analysis
- Administrative boundary visualization (Province, District, Sector, Cell, Village levels)
- Facility detail inspection via interactive identify/popup forms
- Professional mapping for business development and market intelligence

## Contents

```
eFiche_Healthcare_GIS/
├── eFiche_Healthcare_GIS.qgz          ← QGIS Project file (main deliverable)
├── data/
│   ├── efiche_facilities.gpkg         ← GeoPackage with all facility layers
│   ├── facility_registry.csv          ← Master facility registry (all sources merged)
│   ├── osm_facilities_raw.csv         ← Raw OpenStreetMap data
│   └── README.md                       ← This file
└── METADATA.txt                        ← Project metadata and data provenance
```

## Quick Start

### Opening the Project

1. **Install QGIS** (if not already installed)
   - Download from https://www.qgis.org/download
   - Version 3.28+ recommended

2. **Extract the ZIP file** to your desired location

3. **Open QGIS** and select `Project → Open`
   - Navigate to the extracted folder
   - Open `eFiche_Healthcare_GIS.qgz`
   
   OR: Double-click `eFiche_Healthcare_GIS.qgz` file directly

### Exploring the Data

**Layer Structure:**

```
eFiche Healthcare System (root)
├── Healthcare Facilities (grouped layers)
│   ├── Hospitals                    [3 facilities]
│   ├── Health Centres               [2 facilities]
│   ├── Health Posts                 [124 facilities]
│   ├── Clinics                      [2 facilities]
│   ├── Pharmacies                   [1 facility]
│   └── Laboratories                 [1 facility]
└── Reference Data
    └── All Facilities (Combined)     [131 total facilities]
```

**Color Scheme:**
- **Green symbols** = eFiche Client facilities (confirmed partners)
- **Red symbols** = Prospect facilities (non-clients identified via OpenStreetMap)

**Feature Types:**
- Hospitals: Larger star symbols (green/red)
- Health Posts/Centres/Clinics: Smaller circle symbols
- Pharmacies/Laboratories: Specialized symbols with type-specific colors

### Inspecting Facility Details

**Using the Identify Tool:**

1. Click the **Identify Features** tool (hand/pointer icon in toolbar)
2. Click on any facility marker
3. A popup panel opens showing all available attributes

**Available Information:**
- Facility name, type, and classification
- Client/Prospect status
- Location: Province, District, Sector, Cell, Village
- Contact: Phone, Email, Website
- Hours: Opening hours
- Quality: Data source, verification status, data quality rating
- Geographic: Coordinates, address
- Metadata: OSM ID (if applicable), source database

**Using the Attribute Table:**

1. Right-click any layer → **Open Attribute Table**
2. Browse all facilities in spreadsheet format
3. Click any row to highlight on map
4. Filter, sort, or search using column headers

### Map Navigation

- **Zoom:** Mouse wheel or Zoom tools
- **Pan:** Click and drag with mouse
- **Fit to Extent:** Right-click layer → Zoom to Layer
- **Navigate to Facility:** Right-click layer → **Find** → search by name
- **Scale Bar:** Located bottom-left (shows current scale reference)
- **North Arrow:** Located top-right

## Data Sources & Methodology

### eFiche Database
- **Source:** eFiche Automated Healthcare System
- **Records:** 124 facilities
- **Content:** Healthcare facilities registered as eFiche clients or prospects
- **Coverage:** All Rwanda provinces and administrative levels
- **Quality:** Verified and confirmed client facilities

### OpenStreetMap (OSM)
- **Source:** OpenStreetMap (https://openstreetmap.org)
- **Retrieved via:** Overpass API (https://overpass-api.de/)
- **Records:** 7 facilities (after deduplication)
- **Facility Types:** Hospitals, Clinics, Health Centers, Pharmacies, Laboratories
- **Quality:** Crowd-sourced, unverified

### Administrative Boundaries
- **Source:** Rwanda administrative boundary shapefile
- **Level:** Village-level boundaries with hierarchical organization
- **Records:** 14,823 geographic units (Province → District → Sector → Cell → Village)
- **CRS:** WGS84 (EPSG:4326)

## Facility Matching & Deduplication

### Matching Algorithm

Facilities from eFiche and OSM are matched using a weighted scoring system:

```
Match Score = (Name Similarity × 0.4) + (Proximity Score × 0.4) + (Type Similarity × 0.2)
```

**Matching Rules:**
- **Name Match:** Exact match = 1.0, Substring match = 0.8, No match = 0.0
- **Proximity:** Same facility if within ~5km (0.05° lat/lon)
- **Type Match:** Same category = 1.0, Related category = 0.5

**Threshold:** Only records scoring > 0.7 are considered matches

### Deduplication Policy

- ✓ High-confidence matches (score > 0.7): Merged into single record
- ✓ Unmatched OSM records: Added as new "Prospect" facilities
- ✓ eFiche records with coordinates: Retained as "Client" status
- ✓ No data invention: Missing fields shown as "Not Available"

### Result

- **131 total unique facilities**
- **124 eFiche Clients** (verified, registered)
- **7 OSM Prospects** (potential expansion opportunities)

## Data Quality & Verification

### Field Completeness

| Field | eFiche | OSM | Overall |
|-------|--------|-----|---------|
| Facility Name | ✓ Complete | ✓ Complete | 100% |
| Coordinates | ✓ 97% | ✓ 100% | 99% |
| Type/Category | ✓ Complete | ✓ Complete | 100% |
| Phone/Email | ~ Partial | ~ Partial | ~15% |
| Website | ~ Partial | ~ Partial | ~5% |
| Hours | ~ Partial | ~ Partial | ~25% |
| Address | ~ Partial | ~ Limited | ~35% |

### Verification Status

```
eFiche Client      → "Confirmed"      (verified in eFiche database)
OSM Prospect       → "Unverified"     (from external source, requires validation)
Missing Values     → "Not Available"  (no data invented)
```

### Data Quality Ratings

- **Verified:** eFiche-registered facilities with complete location data
- **From External Source:** OSM-derived prospects (lower confidence until verified)
- **Partial:** Facilities with some missing contact information

## Using the GeoPackage Directly

The `efiche_facilities.gpkg` GeoPackage contains all spatial data and can be used independently:

**Layers in GeoPackage:**
- `hospital` - Hospital facilities
- `health_centre` - Health Center facilities
- `health_post` - Health Post facilities
- `clinic` - Clinic facilities
- `pharmacy` - Pharmacy facilities
- `laboratory` - Laboratory facilities
- `all_facilities` - Combined layer of all facility types

**Opening GeoPackage in other tools:**
- QGIS: Add Vector Layer → GeoPackage
- ArcGIS: Data → Geopackages
- Python: `geopandas.read_file('efiche_facilities.gpkg', layer='hospital')`
- PostGIS: `ogr2ogr -f PostgreSQL PG:dbname=mydb efiche_facilities.gpkg`

## Coordinate Reference System

**CRS:** WGS84 (World Geodetic System 1984)
**EPSG Code:** 4326
**Format:** Latitude/Longitude (decimal degrees)
**Datum:** WGS84

All coordinates are referenced to WGS84, enabling easy integration with online mapping services (Google Maps, Bing Maps, Leaflet, Mapbox).

## CSV Registry File

The `facility_registry.csv` contains all merged facility data in tabular format:

```
facility_name,facility_type,province,district,sector,cell,village,latitude,longitude,phone,email,website,opening_hours,address,client_status,source,osm_id,data_quality,verification_status,timestamp
```

This can be imported into spreadsheet or database software for analysis independent of QGIS.

## Refreshing/Updating the Data

### To update OSM data:

1. Re-run the Overpass API query to fetch latest OSM data
2. Process through matching algorithm
3. Regenerate GeoPackage layers
4. Reload project in QGIS (Project → Reload All Layers)

### To add new eFiche facilities:

1. Append new records to source Excel file with same schema
2. Re-run data processing pipeline
3. Update GeoPackage
4. Reload project

### To integrate additional external data sources:

1. Prepare data in WGS84 (EPSG:4326)
2. Implement matching rules for new source
3. Add as new layer in GeoPackage
4. Configure styling in QGIS project

## Layer Styling & Symbols

### Symbol Configuration

**Hospitals:**
- eFiche Clients: Green star (10mm)
- Prospects: Red star (10mm)

**Health Posts/Centres:**
- eFiche Clients: Green circle (5mm)
- Prospects: Red circle (5mm)

**Clinics:**
- eFiche Clients: Green diamond (6mm)
- Prospects: Red diamond (6mm)

**Pharmacies:**
- eFiche Clients: Green square (5mm)
- Prospects: Red square (5mm)

**Laboratories:**
- eFiche Clients: Green triangle (6mm)
- Prospects: Red triangle (6mm)

### Scale-Dependent Rendering

- Zoom in to see facility labels (automatic at appropriate scales)
- Symbols remain visible at all zoom levels
- Map interface shows scale bar for reference

## Popup/Identify Configuration

Clicking any facility displays:

```
FACILITY INFORMATION
─────────────────────
Name:                    [facility_name]
Type:                    [facility_type]
Status:                  [client_status]

LOCATION
─────────────────────
Province:                [province]
District:                [district]
Sector:                  [sector]
Cell:                    [cell]
Village:                 [village]
Address:                 [address]

COORDINATES
─────────────────────
Latitude:                [latitude]
Longitude:               [longitude]

CONTACT INFORMATION
─────────────────────
Phone:                   [phone]
Email:                   [email]
Website:                 [website]
Opening Hours:           [opening_hours]

DATA SOURCE
─────────────────────
Source:                  [source]
Source ID:               [source_id]
OSM ID:                  [osm_id]
Data Quality:            [data_quality]
Verification Status:     [verification_status]
Last Updated:            [timestamp]
```

## Known Limitations

1. **Coordinate Precision:** Some eFiche facilities may have approximate locations (e.g., cell or sector centroid if exact location unavailable)

2. **Contact Information:** Phone/Email/Website data is incomplete; OSM data particularly sparse for contact details

3. **Administrative Boundaries:** Project includes facility-level data; full boundary polygons can be added by importing shapefile version

4. **OSM Coverage:** OpenStreetMap coverage varies by region; rural areas may have fewer mapped facilities

5. **Update Frequency:** This dataset was generated on [TIMESTAMP]. Real-time updates require re-running data processing pipeline

## Project Metadata

- **Project Name:** eFiche Healthcare Market Intelligence System
- **Region:** Rwanda
- **Extent:** -2.84°S to -1.05°S, 28.83°E to 30.90°E
- **CRS:** WGS84 (EPSG:4326)
- **Total Facilities:** 131
- **Data Sources:** 2 (eFiche + OpenStreetMap)
- **Generated:** 2024-08-27
- **QGIS Version:** 3.32.0+
- **GeoPackage Version:** 1.0

## Support & Further Development

### Common Tasks

**Filtering by Client Status:**
- Layer → Filter → `client_status = 'eFiche Client'`

**Exporting to Other Formats:**
- Right-click layer → Export → Choose format (Shapefile, GeoJSON, KML, etc.)

**Creating Thematic Maps:**
- Layer → Symbology → Change to rule-based or expression-based rendering

**Adding New Data:**
- Layer → Create Layer → or Layer → Add Vector Layer

**Measuring Distances:**
- Measure tool in toolbar → click points to measure

**Creating Buffers:**
- Vector → Geoprocessing Tools → Buffer

## Legal & Attribution

**OpenStreetMap:**
- Data © OpenStreetMap contributors
- Licensed under ODbL (Open Data Commons Open Database License)
- Map tiles and services may have additional licenses
- See https://www.openstreetmap.org/copyright

**eFiche Data:**
- Proprietary to Chancen International Rwanda
- Used under data sharing agreement
- Confidentiality and usage restrictions apply

**Project:**
- Created as professional GIS analysis for eFiche system
- Developed by eFiche GIS Team

## Contact & Questions

For questions about:
- **QGIS Usage:** Visit https://www.qgis.org/en/site/about/index.html
- **eFiche System:** Contact Chancen International Rwanda
- **Data Updates:** See "Refreshing/Updating the Data" section above

---

**Last Updated:** 2024-08-27
**Project Status:** Active
**Version:** 1.0
