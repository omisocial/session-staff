# JSON Config + Config Page Design

**Date**: 2026-03-10  
**Scope**: Payroll Rates + Teams  
**Storage**: `localStorage` (no database) + `config.json` defaults  

## Architecture

```
config.json (default values, shipped with app)
     ↓ (first load only)
localStorage['payroll_config'] (persisted user edits)
     ↓
CFG global object (runtime)
     ↓
R, BM, DW, TEAMS (derived constants used by calcWage)
```

## Config Structure

| Section | Fields | Type |
|---------|--------|------|
| `payrollRates.fullShiftWage` | Full shift (>6h) base | number (฿) |
| `payrollRates.bmShortWage` | BM short shift (≤6h) | number (฿) |
| `payrollRates.dwShortWage` | DW short shift (≤6h) | number (฿) |
| `payrollRates.internWage` | Intern flat rate | number (฿) |
| `payrollRates.fullShiftHours` | Full shift quota | number (hrs) |
| `payrollRates.shortShiftHours` | Short shift quota | number (hrs) |
| `payrollRates.shortThreshold` | Short/Full boundary | number (hrs) |
| `payrollRates.otMultiplier` | OT rate multiplier | number |
| `payrollRates.lateRoundBlock` | BM late rounding | number (min) |
| `teams.bm` | BM team codes | string[] |
| `teams.dw` | DW team codes | string[] |
| `teams.internLabels` | Intern labels | string[] |

## Data Flow

1. **App Init** → `loadConfig()` → check `localStorage` → fallback to `fetch('config.json')`
2. **User edits** → Config tab form → `saveConfig()` → `localStorage` + rebuild runtime Sets
3. **Reset** → delete `localStorage` key → re-fetch `config.json`
4. **Export** → download current `CFG` as `.json` file
5. **Import** → read uploaded `.json` → validate → save to `localStorage`

## UI: Config Tab

New sidebar item + pane with two cards:
- **Payroll Rates**: 9 number inputs in a 2-column grid
- **Teams**: 3 textareas (comma-separated team codes)
- Action buttons: Save, Reset, Export, Import
