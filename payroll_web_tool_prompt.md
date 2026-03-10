# 🤖 Implementation Prompt — Part-Time Payroll Web Tool

> Copy and paste this prompt to your developer / AI coding assistant to build the web tool.

---

## PROMPT

You are building a **daily part-time payroll calculator web application** for a Thai warehouse operation. The tool replaces a manual Excel process. Below is the complete specification.

---

### CONTEXT

The company employs part-time workers (collaborators) on a daily basis across two team types:
- **BM teams** (BM1, BM2, BM3, BM4, BMN, BMM)
- **DW teams** (DW1, DW2, DW3, DWN, DWM)

Each day, an admin pastes attendance data (from a checkin/checkout system) into the tool, selects the date and shift, adds any exceptions, and the tool calculates individual wages plus a daily summary.

---

### INPUT DATA FORMAT

The admin provides a list of attendance records. Each record has:

```
full_name        : string   — employee full name
checkin_time     : string   — e.g. "09:03:45", "2026-03-01 09:03:45", "13:00:00 PM"
checkout_time    : string   — e.g. "18:00:00", "17:30", "15:00"
team_code        : string   — "BM1", "BM2", "BM3", "BM4", "BMN", "BMM", "DW1", "DW2", "DW3", "DWN", "DWM", "นักศึกษาฝึกงาน"
nickname         : string   — optional
remarks          : string   — optional free text (see exception handling)
```

The admin also provides (once per sheet/day):
```
shift_range      : string   — e.g. "08:30 - 17:30" or "09:00 - 18:00" or "07:00 - 16:00" or "17:00 - 02:00"
date             : date     — the working date
```

---

### CALCULATION RULES

#### Step 1 — Parse shift

```
shift_start = parse time from left part of shift_range (e.g. "08:30")
shift_end   = parse time from right part of shift_range (e.g. "17:30")

# Handle overnight shifts (e.g. 17:00 - 02:00)
if shift_end < shift_start:
    shift_duration_hours = (shift_end + 24h - shift_start) in hours
else:
    shift_duration_hours = (shift_end - shift_start) in hours
```

#### Step 2 — Standard hours bucket (Q)

```
Q = 5   if shift_duration_hours <= 6
Q = 8   if shift_duration_hours > 6
```

#### Step 3 — Base daily wage (W)

```
is_short_shift = (shift_start >= 09:00) AND (shift_end <= 15:00) AND (shift_end >= shift_start)

if is_short_shift:
    W = 350   if team starts with "DW"
    W = 400   if team starts with "BM"
else:
    W = 500   (all teams)
```

#### Step 4 — Per-minute rate (U)

```
U = W / Q / 60
```

#### Step 5 — Lateness (M, R, S)

```
# Parse checkin to HH:MM only (strip seconds)
actual_checkin = parse_time_HH_MM(checkin_time)

if actual_checkin > shift_start:
    M = (actual_checkin - shift_start) in minutes
else:
    M = 0

# Deduction minutes by team type
if team starts with "BM":
    R = ceil_to_15(M)   # i.e. math.ceil(M / 15) * 15   [or INT((M+14)/15)*15]
elif team starts with "DW":
    R = M               # exact minutes
else:
    R = 0               # interns and others

S = U * R               # late deduction in THB
```

#### Step 6 — Early checkout (T, V)

```
actual_checkout = parse_time_HH_MM(checkout_time)

if actual_checkout < shift_end:
    T = (shift_end - actual_checkout) in minutes
else:
    T = 0

V = T * U               # early leave deduction in THB
```

#### Step 7 — OT (AB, AC)

```
# OT is entered manually per record (not auto-derived from checkin/out)
OT_before_hours  = manual input (default 0)
OT_after_hours   = manual input (default 0)
AB = OT_before_hours + OT_after_hours

AC = (W / Q) * 1.5 * AB    # OT pay
```

#### Step 8 — Manual deductions

```
# Partial leave (entered manually or parsed from remarks)
leave_hours      = manual input (default 0)
leave_deduction  = (W / Q) * leave_hours

# Damaged goods (pre-loaded or entered manually)
damage_deduction = manual input (default 0)
```

#### Step 9 — Total wage

```
total_wage = W - S - V - leave_deduction - damage_deduction + AC

# Note: result may be negative if deductions exceed base wage
# Apply a floor of 0 if desired (confirm with business)
```

---

### EXCEPTION HANDLING

| Exception | Detection | Handling |
|---|---|---|
| Intern / trainee | team_code contains "ฝึกงาน" or "นักศึกษา" | Skip wage calc, display as "-" |
| Late arrival with partial shift note | Remarks contains "เข้างาน" + time | Flag for manual review; use noted time as checkin |
| Partial leave during shift | Remarks contains "ลา" or "หัก X ชั่วโมง" | Auto-parse leave_hours from remark, or prompt admin to confirm |
| No checkout recorded | checkout_time is null/empty | Flag row; do not calculate V (assume full shift) |
| Night shift checkout (cross-midnight) | shift_end < shift_start | Use cross-midnight duration formula above |
| Damage deduction | From damage list import or manual | Prompt admin to confirm amount before applying |
| Checkin time format varies | Various string patterns | Normalize: strip date, remove AM/PM, parse HH:MM |

---

### OUTPUT — Per Employee Row

| Field | Description |
|---|---|
| Name | Full name |
| Team | BM1 / DW1 / etc. |
| Shift | e.g. "08:30 - 17:30" |
| Checkin | Parsed HH:MM |
| Late (min) | M |
| Deducted (min) | R |
| Late deduction (฿) | S |
| Early leave (min) | T |
| Early deduction (฿) | V |
| Base wage (฿) | W |
| OT hours | AB |
| OT pay (฿) | AC |
| Leave deduction (฿) | leave_deduction |
| Damage deduction (฿) | damage_deduction |
| **Total wage (฿)** | **total_wage** |
| Remarks | Flagged notes |

---

### OUTPUT — Daily Summary Block

```
BM1 workers count   = count of rows where team = BM1
BM total wages      = sum of total_wage for all BM teams
BM1 total wages     = sum for BM1 only
BM2–4 total wages   = sum for BM2, BM3, BM4, BMN, BMM

DW1 workers count   = count of rows where team = DW1
DW total wages      = sum of total_wage for all DW teams
DW1 total wages     = sum for DW1 only
```

---

### TECHNICAL REQUIREMENTS

1. **Single-page web app** — no backend required (runs in browser)
2. **Input**: Paste CSV/TSV from clipboard or upload XLSX/CSV file
3. **Date picker**: Select working date → auto-suggest shift type (weekend vs weekday)
4. **Shift selector**: Dropdown for common shifts + custom text input
5. **Inline editing**: Allow admin to override any field (OT hours, leave hours, damage amount) before finalizing
6. **Remarks parser**: Attempt to auto-detect leave hours from remark text (e.g. "หัก 2 ชั่วโมง" → leave_hours = 2)
7. **Export**: Download result as XLSX, matching the original sheet format
8. **Validation warnings**: Flag rows with anomalies (negative total, interns, no checkout, very late)
9. **Localization**: Thai language UI preferred, or bilingual Thai/English

---

### EXAMPLE CALCULATIONS (for unit testing)

#### Test 1 — Normal weekday, on time
```
Input:  name=ธนาธิป, checkin=08:24, checkout=17:30, team=BM1, shift=08:30-17:30
Expect: M=0, R=0, S=0, T=0, V=0, W=500, AB=0, AC=0, total=500
```

#### Test 2 — BM staff 18 minutes late
```
Input:  checkin=08:48, checkout=17:30, team=BM1, shift=08:30-17:30
Expect: M=18, R=30 (ceil to 15), S=30×(500/8/60)=31.25, total=500-31.25=468.75
```

#### Test 3 — DW staff 22 minutes late
```
Input:  checkin=09:22, checkout=18:00, team=DW1, shift=09:00-18:00
Expect: M=22, R=22 (exact), S=22×(500/8/60)=22.92, total=500-22.92=477.08
```

#### Test 4 — Early checkout
```
Input:  checkin=08:25, checkout=15:00, team=BM1, shift=08:30-17:30
Expect: T=150 min, V=150×(500/8/60)=156.25, total=500-156.25=343.75
```

#### Test 5 — OT 2 hours after shift
```
Input:  team=BM1, shift=09:00-18:00, W=500, Q=8, OT_after=2
Expect: AC=(500/8)×1.5×2=187.5, total=500+187.5=687.5
```

#### Test 6 — Partial leave 2 hours
```
Input:  team=BM1, W=500, Q=8, leave_hours=2
Expect: leave_deduction=62.5×2=125, total=500-125=375
```

#### Test 7 — Short shift (BM, half-day)
```
Input:  shift=09:00-13:00, team=BM1
Expect: duration=4hrs → Q=5, is_short_shift=true → W=400, U=400/5/60=1.333
```

#### Test 8 — Night shift no late
```
Input:  checkin=17:00, checkout=02:00+1, team=BM1, shift=17:00-02:00
Expect: duration=9hrs → Q=8, W=500 (shift_end 02:00 < 15:00 but shift_end < shift_start → is_short_shift=false), M=0, total=500
```

---

### STACK SUGGESTIONS

- **Vanilla JS + HTML/CSS** (simplest, fully browser-based)
- **React + Tailwind** (if richer UI needed)
- **SheetJS (xlsx)** for reading/writing Excel files
- **Day.js or date-fns** for time parsing

---

*End of implementation prompt.*
