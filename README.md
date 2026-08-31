# Camera Inventory Database (Microsoft Access)

Relational database project for coursework: a camera inventory system built in Microsoft Access.

## Schema

| Table | Purpose | Primary Key |
|---|---|---|
| tblBuilding | Lookup — campus buildings | BuildingID |
| tblLevelCode | Lookup — floor levels (B, 1, 2, 3) | LevelCode |
| tblFrameOrGrid | Lookup — frame/grid columns (A–D) | FrameOrGrid |
| tblCameras | Main table — one record per camera | CameraID (AutoNumber) |

## Features

- **Concatenation query** — builds full camera labels (`Building-Level-Frame-Number`, e.g. `ADM-1-A-01`) with `qryCameraLabels` (SQL in the guide, Section 6.2)
- **Validation rules** — CameraNumber 1–999, Status limited to Active/Inactive/Maintenance, InstallDate cannot be future
- **Lookup tables** — combo-box drop-downs on BuildingID, LevelCode, FrameOrGrid (table-based) and Status (value list), all Limit To List
- **Relationships** — three one-to-many joins with referential integrity enforced
- **Reports** — camera inventory grouped by building with counts

## Files

- `CameraInventory.accdb` — the Access database (tables, data, validation rules, lookups, relationships)
- `Camera_Inventory_Access_Guide.md` — full build guide including the two steps done inside Access (concatenation query + report)
- `seed_data/` — CSV files with sample data for importing
