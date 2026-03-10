# 📋 Part-Time Payroll Calculation — Process Specification

> **Source file:** `รายชื่อพนักงานเดือนมีนาคม.xlsx`
> **Scope:** Daily wage calculation for part-time/collaborator staff (BM and DW teams)
> **Purpose:** Spec + prompt for automating via web tool
>
> **Validation status:** All rules reverse-engineered from actual Excel computed values.
> Engine (`payroll_engine.py`) validated against 8 daily sheets — **347/348 rows match** (99.7%).
> The 1 non-match is a confirmed Excel bug (see Section 15), not a logic error.

---

## 1. System Overview

### 1.1 Key Sheets

| Sheet | Purpose |
|---|---|
| `Members` | Employee master data: name, nickname, bank account, team (สังกัด) |
| `วันที่ X` | One sheet per working day — attendance input + auto calculation |
| `วันที่ X กะเช้า / กะดึก` | Split sheets when a day has morning & night shifts |
| `Result` | Monthly summary: wage per day per person |
| `สินค้าเสียหาย` | Damaged goods deduction tracker |

### 1.2 Employee Types (Note / สังกัด column)

| Code | Group | Description |
|---|---|---|
| BM1, BM2, BM3, BM4, BMN, BMM | BM teams | Warehouse/packing staff (Bangkok) |
| DW1, DW2, DW3, DWN, DWM | DW teams | Warehouse staff (different zone) |
| นักศึกษาฝึกงาน / ฝึกงาน | Interns | **No wage calculated** |

---

## 2. Input Columns (Per Daily Sheet)

| Column | Field | Type | Notes |
|---|---|---|---|
| A | ลำดับ | Number | Sequence |
| B | ชื่อ-นามสกุล | Text | Full name (lookup key) |
| C | Checkin time | Datetime | Actual scan-in timestamp |
| D | Checkout time | Time/Datetime/Text | Actual scan-out (may be string or time only) |
| E | Note | Text | Team code: BM1, DW1, etc. |
| F | ชื่อเล่น | Text | Nickname |
| G | หมายเหตุ | Text | **Free-text exception notes** (e.g. leave, late entry, partial shift) |
| N | กะการทำงาน | Text | Shift range, e.g. `"09:00 - 18:00"` |

---

## 3. Shift Types

### 3.1 Standard Shifts

| Shift Name | Time Range | Day Type | Duration |
|---|---|---|---|
| Weekday shift | `08:30 - 17:30` | Mon–Fri | 9 hrs |
| Weekend shift | `09:00 - 18:00` | Sat–Sun | 9 hrs |
| Morning shift | `07:00 - 16:00` | Special | 9 hrs |
| Night shift | `17:00 - 02:00` | Special | 9 hrs (overnight) |
| Short shift | e.g. `09:00 - 13:00` | Half-day | ≤ 6 hrs |

> **How the system detects shift duration** (night shift handled by cross-midnight check):
> ```
> duration_hours = (end < start) ? (end + 24 - start) : (end - start)
> ```

### 3.2 Standard Hours (Q) — "Paid Hours Bucket"

Used to calculate **hourly/per-minute rate**, NOT actual hours worked.

```
Q = (shift duration ≤ 6 hours) → 5
Q = (shift duration > 6 hours) → 8
```

*Example: 08:30–17:30 = 9 hrs → Q = 8. This reflects 9 hrs with 1 hr unpaid lunch = 8 paid hours.*

---

## 4. Base Daily Wage (W)

```
IF shift_start >= 09:00 AND shift_end <= 15:00:
    W = 350  (DW team)
    W = 400  (BM team)
ELSE:
    W = 500  (all teams)
```

**In plain terms:**
- Any full-length shift (≥ 9 hrs, ending after 15:00) = **500 THB/day**
- Short shifts entirely within 09:00–15:00 window = **400 THB** (BM) or **350 THB** (DW)

---

## 5. Derived Rates

| Variable | Formula | Example (W=500, Q=8) |
|---|---|---|
| Hourly rate | `W / Q` | 500/8 = **62.5 THB/hr** |
| Per-minute rate (U) | `W / Q / 60` | 500/8/60 = **1.0417 THB/min** |

---

## 6. Lateness Calculation

### Step 1: Calculate raw late minutes (M)

```
actual_checkin = TIMEVALUE(checkin)   [HH:MM only, seconds ignored]
shift_start    = TIMEVALUE(shift left part)

M = (actual_checkin > shift_start) ? (actual_checkin - shift_start) * 1440 : 0
```

*If checkin is before or exactly at shift start → M = 0 (not late).*

### Step 2: Deduction minutes (R) — rounding rule by team

```
BM teams:
    R = (M > 0) ? CEIL_15(M) : 0
    where CEIL_15(x) = INT((x + 14) / 15) * 15
    → rounds UP to next 15-minute block
    → 1–15 min late = 15 min deducted
    → 16–30 min late = 30 min deducted

DW teams:
    R = M   (exact minutes, no rounding)

Interns / others:
    R = 0   (no deduction)
```

### Step 3: Late deduction amount (S)

```
S = (W / Q / 60) * R  =  per_minute_rate × deduction_minutes
```

---

## 7. Early Checkout Calculation

### Step 1: Minutes left early (T)

```
T = (checkout < shift_end) ? (shift_end - checkout) * 1440 : 0
```

*If checkout at or after shift end → T = 0 (no deduction).*

### Step 2: Early leave deduction amount (V)

```
V = T × (W / Q / 60)  =  per_minute_rate × early_minutes
```

---

## 8. Overtime (OT)

OT is entered **manually** in two sub-columns per OT block:
- **Col X / Z**: OT time range as text (e.g. `"16:30-19:30"`)
- **Col Y / AA**: OT hours as a number (e.g. `3`)

```
OT_before_shift_hrs  = Y   (hours worked before shift start)
OT_after_shift_hrs   = AA  (hours worked after shift end)
Total_OT_hrs (AB)    = Y + AA

OT Pay (AC) = (W / Q) * 1.5 * AB
            = hourly_rate × 1.5 × total_OT_hours
```

*OT rate = 1.5× base hourly rate, regardless of team or day type.*

---

## 9. Special Deductions

### 9.1 Leave Deduction (manual — noted in column G)

When an employee takes partial leave during the shift (e.g. *"หัก 2 ชั่วโมง ลางานช่วง 13.00–15.00"*):

- Entered manually in a dedicated column (AD/AE depending on sheet version)
- `leave_hours` = number of hours absent
- `leave_deduction` = hourly_rate × leave_hours = (W/Q) × leave_hours

```
leave_deduction = (500 / 8) × leave_hours = 62.5 × leave_hours
```

### 9.2 Damaged Goods Deduction (สินค้าเสียหาย)

- Tracked separately in the `สินค้าเสียหาย` sheet
- `damage_amount` = manually entered THB value
- Applied in column AG/AE per daily sheet when it falls on that day

---

## 10. Final Wage Formula

```
total_wage = W - S - V - leave_deduction - damage_deduction + AC

Where:
  W   = base daily wage (500 / 400 / 350)
  S   = late deduction (THB)
  V   = early checkout deduction (THB)
  leave_deduction = (W/Q) × leave_hours  [if any, 0 otherwise]
  damage_deduction = damaged goods amount [if any, 0 otherwise]
  AC  = OT pay
```

---

## 11. Daily Sheet Summary (top of each sheet)

Auto-calculated summary block at the top of each daily sheet:

| Metric | Formula |
|---|---|
| Count BM1 workers | `COUNTIF(E:E, "*BM1*")` |
| Count DW1 workers | `COUNTIF(E:E, "*DW1*")` |
| Total wages BM1 | `SUMIF(E:E, "*BM1*", AF:AF)` |
| Total wages DW | `SUMIF(E:E, "*DW*", AF:AF)` |
| Total wages BM 2–4 | `SUM(BM2 + BM3 + BM4 + BMN + BMM)` |

---

## 12. Cases Breakdown

### ✅ Happy Cases

#### Case 1 — Normal Weekday, On Time, Full Day (Most Common)
- Shift: `08:30 - 17:30`
- Check-in: e.g. `08:25`  → not late → M=0, R=0, S=0
- Check-out: `17:30` or after → T=0, V=0
- No OT, no leave, no damage
- **Result: W = 500 THB**

#### Case 2 — Normal Weekend, On Time, Full Day
- Shift: `09:00 - 18:00`
- Same logic as Case 1
- **Result: W = 500 THB**

#### Case 3 — BM Staff Late (Weekday or Weekend)
- Shift: `08:30 - 17:30`, Checkin: `08:45`
- M = (08:45 − 08:30) × 1440 = 15 min
- R = CEIL_15(15) = 15 min
- S = 1.0417 × 15 = **15.625 THB** deducted
- **Result: 500 − 15.625 = 484.375 THB**

#### Case 4 — DW Staff Late (Exact Deduction)
- Shift: `09:00 - 18:00`, Checkin: `09:22`
- M = 22 min, R = 22 min (exact for DW)
- S = 1.0417 × 22 = **22.92 THB** deducted

#### Case 5 — Staff with OT After Shift
- Shift ends `18:00`, works until `19:30` → OT = 1.5 hrs
- AC = (500/8) × 1.5 × 1.5 = **140.625 THB**
- **Result: 500 + 140.625 = 640.625 THB**

#### Case 6 — Short Shift (Half-Day)
- Shift: `09:00 - 13:00`, Duration = 4 hrs → Q = 5
- BM team: W = 400, DW team: W = 350
- Per-minute rate = 400/5/60 = 1.333 THB/min

#### Case 7 — Night Shift
- Shift: `17:00 - 02:00` (overnight, 9 hrs) → Q = 8
- W = 500 (full shift, end time 02:00 > 15:00 in cross-midnight logic)

---

### ⚠️ Exception Cases

#### EX-1 — Early Checkout (Left Before Shift End)
- Checkout at `15:00`, Shift ends `18:00` → T = 180 min
- V = 1.0417 × 180 = **187.5 THB** deducted
- Note: system flags this in column I: *"ออกก่อน X นาที"*

#### EX-2 — Partial Absence / Leave Mid-Day
- Noted in G: *"หัก 2 ชั่วโมง ลางานช่วง 13.00–15.00"*
- `leave_hours` = 2 entered manually
- `leave_deduction` = 62.5 × 2 = **125 THB**
- Checkout still shows 18:00 (present physically but partially absent)

#### EX-3 — Combined Late + Leave + Damage
- All three deductions stack:
  - S (late) + V (early) + leave_deduction + damage_deduction all subtract from W
- Result can be less than 0 in theory — system does not cap at 0 (no floor in formula)

#### EX-4 — Afternoon Entry (เข้างานช่วงบ่าย)
- Check-in recorded as `12:00` with G note: *"เข้างานช่วงบ่าย"*
- L column will show late status; M = (12:00 − shift_start) × 1440
- BM: R = CEIL_15(M), very large deduction
- **Often handled as a separate short-shift arrangement** — requires manual review

#### EX-5 — Checkin Timestamp Format Inconsistency
- Checkin (C) can appear as: full datetime, time-only string, or text like `"13:00:00 PM"`
- System uses `TEXT(C,"hh:mm:ss")` then `TIMEVALUE(LEFT(...,5))` to normalize
- Web tool must handle all formats

#### EX-6 — Missing Checkout / Checkout Before Shift End Already Set
- If Checkout (D) is entered as the exact shift end (typed as `"18:00:00"` string), T = 0
- If Checkout is a `datetime.time` object vs string, parsing differs

#### EX-7 — Damaged Goods Deduction Applied
- Cross-referenced from `สินค้าเสียหาย` sheet by name
- Manual entry per day — the admin decides which day to deduct
- System does not auto-match by date

#### EX-8 — Night Shift Overtime (Cross-midnight T calculation)
- Shift: `17:00 - 02:00`
- Checkout at `02:30` → OT after = 0.5 hrs
- T (early check) formula: `IF(D < P, ...)` — when D=02:30 and P=02:00 as time values, D > P correctly → T=0 ✓
- But if checkout is `01:45` → D < P → T = 15 min early checkout deduction ✓

#### EX-9 — OT Before Shift
- Entered in column X/Y (before shift OT)
- Same formula: AC includes Y hours
- Used when staff arrives very early and works before official shift start

#### EX-10 — Intern Rows (No Wage)
- Note column E = "นักศึกษาฝึกงาน" or "ฝึกงาน"
- Not BM or DW → W formula returns 500 (no team guard), but typically rows are excluded from summaries
- **Web tool should skip wage calculation for interns**

---

## 13. Data Flow Summary

```
INPUT (per employee per day):
  - Full name (B)
  - Checkin time (C)
  - Checkout time (D)
  - Team code (E)
  - Shift range (N)  ← usually fixed per day for all staff
  - Remarks/G        ← optional: leave, notes

AUTO-CALCULATED:
  Step 1: Parse shift start (O) and end (P) from N
  Step 2: Calculate shift duration → Q (5 or 8)
  Step 3: Determine W (base wage: 500, 400, or 350)
  Step 4: Derive per-minute rate U = W/Q/60
  Step 5: Calculate late minutes M → deduction minutes R → money S
  Step 6: Calculate early exit minutes T → money V
  Step 7: Sum OT hours AB → OT pay AC
  Step 8: Apply leave deduction (manual from G notes)
  Step 9: Apply damage deduction (from สินค้าเสียหาย)
  Step 10: Total = W − S − V − leave − damage + AC

OUTPUT:
  - Total wage per employee per day
  - Summary by team (BM1, BM2..., DW1, DW2...)
  - Feed into Result sheet monthly totals
```

---

## 14. Questions Requiring Clarification Before Implementation

Before building the web tool, the following ambiguities should be confirmed:

1. **Intern rows**: Should the system completely skip wage computation, or flag them as ฿0?
2. **Damage deduction application date**: Is it always the same day as the error, or does the admin choose the date?
3. **Minimum wage floor**: If deductions exceed base wage, should result be 0 (floor) or negative (as in current Excel)?
4. **BM late rounding grace**: Is there any grace period (e.g., first 5 minutes not penalized), or does even 1 minute trigger a 15-min deduction?
5. **Short-shift rate for DW on non-standard hours**: If a DW worker does a 5-hour shift starting at 07:00 (ends 12:00, not in 09:00–15:00 window), does W = 500?
6. **OT manual entry**: In the web tool, will OT hours still be entered manually, or should the tool auto-calculate from checkout time?
7. **Multiple shifts per day (กะเช้า/กะดึก)**: Should the web tool support split-day sheets as separate submissions?

---

## 15. Known Excel Bugs (Improvements in Web Tool)

The following issues were discovered during code-based reverse-engineering and validation. The web tool should handle these **correctly** (unlike the original Excel):

### Bug 1 — Datetime vs. Time-Only Checkout Comparison

**Symptom:** When the checkout time is stored as a full datetime (e.g., `2026-03-01 15:04:26`) while the shift-end column is stored as time-only (`18:00:00`), Excel's `IF(D < P, ...)` formula silently returns 0 because a datetime serial number (~45,672) is always greater than a time fraction (0.75 = 18:00).

**Effect:** Workers who leave early but whose checkout was captured as a full datetime receive full pay with no deduction.

**Confirmed case:** Sheet วันที่ 1, employee พิชิต หัสดา (DW3) — checkout at 15:04 (datetime format), Excel shows T=0, total=500 THB. Correct calculation: T=176 min, V≈183.33 THB, total≈316.67 THB.

**Web tool fix:** Always normalize checkout to HH:MM regardless of input format before comparison.

### Bug 2 — Intern Formula Error (`#VALUE!`)

**Symptom:** The lateness formula uses `SEARCH("BM", E9)` nested in `IF`. For team codes that contain neither "BM" nor "DW" (e.g., `นักศึกษาฝึกงาน`), `SEARCH()` returns `#VALUE!` which propagates through all downstream cells.

**Effect:** Intern rows display `#VALUE!` in all calculated columns rather than a clean "N/A" or 0.

**Web tool fix:** Detect intern rows first (team_code contains `ฝึกงาน` or `นักศึกษา`) and skip wage calculation entirely, displaying `"-"` in wage columns.

---

## 16. Validation Results Summary

Engine (`payroll_engine.py`) was validated by running against the recalculated March 2026 Excel workbook (LibreOffice headless recalculation).

| Sheet | Rows matched | BM total match | DW total match | Notes |
|---|---|---|---|---|
| วันที่ 1 | 64/65 | ✅ 23,046.88 | ❌ 183.33 diff | 1 case: datetime checkout bug (Excel error, not engine error) |
| วันที่ 2 | 61/61 | ✅ 22,546.88 | ✅ 6,967.71 | Perfect |
| วันที่ 3 | 77/77 | ✅ 32,218.75 | ✅ 7,857.29 | OT before shift + leave deductions |
| วันที่ 7 | 34/34 | ✅ 13,125.00 | ✅ 3,478.13 | Weekend shift |
| วันที่ 8 | 39/39 | ✅ 15,812.50 | ✅ 2,988.54 | Perfect |
| วันที่ 4 กะเช้า | 62/62 | ✅ 31,140.63 | ✅ 12,196.88 | Morning shift |
| วันที่ 4 กะดึก | 10/10 | ✅ 6,109.38 | ✅ 0.00 | Night shift |
| **TOTAL** | **347/348** | | | **99.7% match** |

All salary totals (BM + DW) match the Excel summary block values to the nearest satang in 7 of 7 sheets (excluding the one sheet where Excel itself has the datetime bug).

---

*Document generated from formula reverse-engineering of the March 2026 payroll Excel file.*
*Validation performed: 2026-03-10*
