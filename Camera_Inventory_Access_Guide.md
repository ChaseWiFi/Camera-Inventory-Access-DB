# Camera Inventory Database — Microsoft Access Guide

**Project:** Camera Inventory Relational Database
**Platform:** Microsoft Access (2016 / 2019 / 365)
**File provided:** `CameraInventory.accdb` — a ready-to-open Access database

---

## 0. What Is Already Built Into CameraInventory.accdb

The database file already contains:

- **4 tables** — `tblBuilding` (5 rows), `tblLevelCode` (4 rows), `tblFrameOrGrid` (4 rows), `tblCameras` (10 rows), all with primary keys
- **Validation rules** — CameraNumber (`>0 And <=999`), Status (`In("Active","Inactive","Maintenance")`), InstallDate (`<=Date()`), with error messages; Required=Yes on the key fields
- **Lookup drop-downs** — BuildingID, LevelCode, and FrameOrGrid pull from their lookup tables; Status is a value-list combo box, all with Limit To List on
- **Relationships** — all three one-to-many joins with referential integrity enforced

**The two things you still create inside Access (about 5 minutes):**

1. **The concatenation query** — Section 6 below. Open Query Design, switch to SQL View, paste the SQL, save as `qryCameraLabels`.
2. **The report** — Section 8 below. Two minutes with the Report Wizard.

Queries and reports are stored by Access in internal system objects that can only be written by Access itself — that's why they're the remaining steps.

---

## 1. Database Design Overview

### Schema (Entity Structure)

| Table | Purpose | Primary Key |
|---|---|---|
| `tblBuilding` | Lookup — list of buildings on campus | `BuildingID` |
| `tblLevelCode` | Lookup — floor levels (basement, 1st, 2nd, etc.) | `LevelCode` |
| `tblFrameOrGrid` | Lookup — frame or grid reference columns | `FrameOrGrid` |
| `tblCameras` | Main data table — one record per camera | `CameraID` |
| `tblCameras` (lookups) | Foreign keys: `BuildingID`, `LevelCode`, `FrameOrGrid` | — |

### Relationships (One-to-Many)

```
tblBuilding  ──1:N──▶ tblCameras ◀──1:N──  tblLevelCode
tblFrameOrGrid ──1:N──▶ tblCameras
```

- One **Building** has many cameras.
- One **LevelCode** (floor) has many cameras.
- One **FrameOrGrid** (frame/grid column) has many cameras.
- The **CameraID** is a concatenated label built from the four parts: `Building – LevelCode – FrameOrGrid – CameraNumber` (e.g., `ADM-2-A-14`).

---

## 2. Create the Database File

1. Open Access → **File → New → Blank database**.
2. Name it `CameraInventory.accdb` and save it to your course folder.
3. If you see the yellow **Security Warning** bar at the top, click **Enable Content**.

---

## 3. Build the Lookup Tables First

### 3.1 `tblBuilding`

| Field Name | Data Type | Field Properties |
|---|---|---|
| BuildingID | Short Text (3 chars) | Primary Key — e.g., `ADM` |
| BuildingName | Short Text (50) | Required = Yes |

**Sample data:**

| BuildingID | BuildingName |
|---|---|
| ADM | Administration Building |
| ART | Fine Arts Center |
| LIB | Learning Resource Center |
| SCI | Science & Engineering Building |
| TEC | Technology Center |

### 3.2 `tblLevelCode`

| Field Name | Data Type | Field Properties |
|---|---|---|
| LevelCode | Short Text (1 char) | Primary Key |
| LevelDescription | Short Text (30) | Required = Yes |

**Sample data:**

| LevelCode | LevelDescription |
|---|---|
| B | Basement |
| 1 | First Floor |
| 2 | Second Floor |
| 3 | Third Floor |

### 3.3 `tblFrameOrGrid`

| Field Name | Data Type | Field Properties |
|---|---|---|
| FrameOrGrid | Short Text (1 char) | Primary Key |
| Description | Short Text (30) | Required = Yes |

**Sample data:**

| FrameOrGrid | Description |
|---|---|
| A | Frame/Grid Column A |
| B | Frame/Grid Column B |
| C | Frame/Grid Column C |
| D | Frame/Grid Column D |

**To create each table:**
1. **Create → Table Design**.
2. Type the field names and set data types as shown.
3. Select the key field → **Design tab → Primary Key**.
4. Save as `tblBuilding` / `tblLevelCode` / `tblFrameOrGrid`.
5. Switch to **Datasheet View** and type the sample data (or import the included CSV files — see Section 9).

---

## 4. Build the Main Table `tblCameras`

**Create → Table Design** with these fields:

| Field Name | Data Type | Field Properties |
|---|---|---|
| CameraID | AutoNumber | Primary Key |
| BuildingID | Short Text (3) | Required = Yes; **Lookup** (see 4.1) |
| LevelCode | Short Text (1) | Required = Yes; **Lookup** |
| FrameOrGrid | Short Text (1) | Required = Yes; **Lookup** |
| CameraNumber | Number (Integer) | **Validation Rule:** `>0 And <=999` |
| Location | Short Text (50) | Optional |
| Status | Short Text (10) | **Validation Rule:** `In("Active","Inactive","Maintenance")` |
| InstallDate | Date/Time | Format: Short Date |
| Notes | Long Text | Optional |

### 4.1 Validation Rules (copy/paste into Field Properties)

Select the field in Design View, then in the **Field Properties** pane:

**CameraNumber:**
- Validation Rule: `>0 And <=999`
- Validation Text: `"Camera number must be between 1 and 999."`

**Status:**
- Validation Rule: `In("Active","Inactive","Maintenance")`
- Validation Text: `"Status must be Active, Inactive, or Maintenance."`

**InstallDate:**
- Validation Rule: `<=Date()`
- Validation Text: `"Install date cannot be in the future."`

### 4.2 Lookup Fields (drop-down lists)

For `BuildingID`, `LevelCode`, and `FrameOrGrid`:

1. In Design View, click the field's **Data Type** dropdown → choose **Lookup Wizard…**
2. Select **"I want the lookup field to get values from another table or query."**
3. Choose the matching lookup table (`tblBuilding`, `tblLevelCode`, or `tblFrameOrGrid`).
4. Add the ID/description fields to the lookup → Next.
5. Choose to **hide the key column** (recommended) so only the friendly value shows.
6. Accept **"Enable data integrity"** (Referential Integrity ON) → Finish.
7. In Field Properties → **Lookup tab**, confirm `Row Source`, `Bound Column`, and `Limit To List = Yes`.

### 4.3 Sample data (10 rows)

| BuildingID | LevelCode | FrameOrGrid | CameraNumber | Location | Status | InstallDate |
|---|---|---|---|---|---|---|
| ADM | 1 | A | 1 | Main Lobby | Active | 2024-01-15 |
| ADM | 2 | B | 2 | East Hallway | Active | 2024-02-20 |
| LIB | 1 | C | 3 | Entrance | Active | 2024-03-05 |
| LIB | 2 | A | 4 | Study Area | Maintenance | 2024-06-10 |
| SCI | 1 | D | 5 | Lab Wing | Active | 2024-04-18 |
| SCI | 3 | B | 6 | Rooftop Access | Inactive | 2023-11-02 |
| TEC | 1 | A | 7 | Server Room Door | Active | 2024-05-25 |
| TEC | 2 | C | 8 | Corridor North | Active | 2024-07-01 |
| ART | 1 | B | 9 | Gallery | Active | 2024-08-12 |
| ART | B | D | 10 | Storage | Inactive | 2023-09-30 |

---

## 5. Set Up Relationships

1. **Database Tools → Relationships**.
2. Add all four tables.
3. Drag `BuildingID` from `tblBuilding` onto `BuildingID` in `tblCameras`.
4. In the Edit Relationships dialog, check **Enforce Referential Integrity**, then click **Create**.
5. Repeat for `LevelCode` and `FrameOrGrid`.
6. Save and close the Relationships window. You should see three 1-to-many lines.

---

## 6. Concatenation Query

This is the heart of the assignment: combining the four fields into one full camera ID.

### 6.1 Build it in Design View

1. **Create → Query Design**; add `tblCameras`, `tblBuilding`, `tblLevelCode`, `tblFrameOrGrid` (relationships should auto-join).
2. Add these fields to the grid: `CameraID` (from tblCameras), `BuildingID` (from tblBuilding), `LevelCode`, `FrameOrGrid`, `CameraNumber`.
3. In the first empty column, click the **Field** row and paste:

```
CameraLabel: [tblBuilding].[BuildingID] & "-" & [LevelCode] & "-" & [FrameOrGrid] & "-" & Format([CameraNumber],"00")
```

4. Run the query (red **!** button). Example output: `ADM-1-A-01`, `LIB-2-A-04`.
5. Save as `qryCameraLabels`.

### 6.2 SQL View (paste directly if you prefer)

```sql
SELECT tblCameras.CameraID,
       tblBuilding.BuildingName,
       tblBuilding.BuildingID & "-" & tblCameras.LevelCode & "-" &
       tblCameras.FrameOrGrid & "-" & Format(tblCameras.CameraNumber,"00") AS CameraLabel,
       tblCameras.Status,
       tblCameras.Location
FROM tblFrameOrGrid
INNER JOIN (tblLevelCode
INNER JOIN (tblBuilding
INNER JOIN tblCameras
       ON tblBuilding.BuildingID = tblCameras.BuildingID)
       ON tblLevelCode.LevelCode = tblCameras.LevelCode)
       ON tblFrameOrGrid.FrameOrGrid = tblCameras.FrameOrGrid;
```

### 6.3 Bonus queries

**Count cameras per building:**

```sql
SELECT tblBuilding.BuildingName,
       Count(tblCameras.CameraID) AS NumberOfCameras
FROM tblBuilding
INNER JOIN tblCameras ON tblBuilding.BuildingID = tblCameras.BuildingID
GROUP BY tblBuilding.BuildingName;
```

**Parameter query (user types a building code):**

```sql
PARAMETERS [Enter Building Code] Text(3);
SELECT tblBuilding.BuildingID & "-" & tblCameras.LevelCode & "-" &
       tblCameras.FrameOrGrid & "-" & Format(tblCameras.CameraNumber,"00") AS CameraLabel,
       tblCameras.Location, tblCameras.Status
FROM tblBuilding
INNER JOIN tblCameras ON tblBuilding.BuildingID = tblCameras.BuildingID
WHERE tblBuilding.BuildingID = [Enter Building Code];
```

---

## 7. Create a Data-Entry Form (recommended)

1. **Create → Form Wizard**.
2. Fields: all from `tblCameras`; click **Next**.
3. Layout: **Columnar** → name it `frmCameras`.
4. The lookup fields automatically become drop-downs on the form.

---

## 8. Create the Report

### 8.1 Camera Inventory Report (grouped by building)

1. **Create → Report Wizard**.
2. Base it on **`qryCameraLabels`** (so the concatenated `CameraLabel` appears).
3. Add fields: `BuildingName`, `CameraLabel`, `Location`, `Status`.
4. Grouping: **BuildingName**.
5. Sort: **CameraLabel** (ascending).
6. Layout: **Stepped**, Portrait orientation.
7. Title: `rptCameraInventory` → **Finish**.
8. In Layout View, add a header title and totals: **Design tab → Group & Sort → Add a group footer → insert a Count(*) text box** to show cameras per building.

### 8.2 Status Summary Report

1. Report Wizard based on `tblCameras`.
2. Fields: `Status`; use grouping on `Status` and a count.

---

## 9. Import the Seed Data (optional shortcut)

Instead of typing sample rows, import the included CSV files:

1. **External Data → New Data Source → From File → Text File**.
2. Browse to the CSV → choose **"Append a copy of the records to the table"** and select the matching table.
3. In the Import Wizard, choose **Delimited → Comma**, check **First Row Contains Field Names** → Finish.

Files provided:
- `tblBuilding.csv` → append to `tblBuilding`
- `tblLevelCode.csv` → append to `tblLevelCode`
- `tblFrameOrGrid.csv` → append to `tblFrameOrGrid`
- `tblCameras.csv` → append to `tblCameras`

---

## 10. Validation Rules — Quick Reference

| Where | Rule | Message |
|---|---|---|
| CameraNumber | `>0 And <=999` | Camera number must be between 1 and 999 |
| Status | `In("Active","Inactive","Maintenance")` | Choose Active, Inactive, or Maintenance |
| InstallDate | `<=Date()` | Install date cannot be in the future |
| Lookups | Limit To List = Yes | Prevents typing values that aren't in the lookup table |
| Referential integrity (Relationships) | Enforced on all 3 FK links | Prevents orphaned camera records |

---

## 11. Submission Checklist

- [ ] `CameraInventory.accdb` opens with content enabled
- [ ] 4 tables with correct primary keys
- [ ] 3 lookup tables populated; lookup drop-downs on `tblCameras`
- [ ] Validation rules on `CameraNumber`, `Status`, `InstallDate`
- [ ] Relationships enforced (referential integrity on all joins)
- [ ] `qryCameraLabels` concatenation query runs (e.g., `TEC-2-C-08`)
- [ ] `rptCameraInventory` report grouped by building with counts
- [ ] Screenshot Relationships window for your instructor (if required)
