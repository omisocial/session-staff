# 📘 Daily Payroll Calculator — Staff User Guide

> **Application:** Session Staff Payroll Tool  
> **Version:** 1.0 · **Last Updated:** March 2026  
> **Audience:** Warehouse Admin / Payroll Staff

---

## 📋 Quick Reference

| Item | Details |
|------|---------|
| **URL** | Open `payroll_tool.html` in your browser |
| **Login** | Username: `Automation` · Password: `@1m!nd` |
| **Supported Languages** | 🇹🇭 Thai · 🇬🇧 English · 🇻🇳 Vietnamese |
| **Employee Types** | BM teams (BM1–BMM) · DW teams (DW1–DWM) · Interns |
| **Input** | Manual entry · Paste from clipboard · Import Excel (.xlsx/.xls) |
| **Output** | On-screen results table · CSV export · Excel export (.xlsx) · Print |

---

## 1. Logging In

1. Open the Payroll Tool in your web browser.
2. You will see the **login screen** with a 💰 icon.
3. Enter:
   - **Username:** `Automation`
   - **Password:** `@1m!nd`
4. Click **Sign In**.

> **Note:** Your session is remembered until you close the browser tab. You won't need to log in again if you refresh the page.

### First-Time Onboarding

On your first login, a **Welcome popup** will appear with a 3-step walkthrough:

| Step | Content |
|------|---------|
| 1 — Welcome | Overview of the tool's capabilities |
| 2 — How to Use | Step-by-step workflow summary |
| 3 — Ready to Go | Output options (CSV, print) |

Click **Next →** to proceed or **Skip** to dismiss. The popup won't appear again.

---

## 2. Setting Up the Shift

The **⚙️ Shift Settings** card is at the top of the page.

### 2.1 Select a Date

- Click the **Date** input and pick the working date.
- Or press the **Today** button to auto-fill today's date.
- The header will update to show the formatted date and shift times.

### 2.2 Choose a Shift Preset

Select from the **Shift (Preset)** dropdown:

| Preset | Time Range | Duration |
|--------|-----------|----------|
| Regular | 08:30 – 17:30 | 9 hrs |
| Late | 09:00 – 18:00 | 9 hrs |
| Short | 09:00 – 15:00 | 6 hrs |
| Late-Short | 10:00 – 16:00 | 6 hrs |
| Early-Short | 07:00 – 12:00 | 5 hrs |
| Night | 17:00 – 02:00 | 9 hrs (overnight) |

### 2.3 Custom Shift

You can manually adjust **Shift Start** and **Shift End** times. When you edit these fields, the preset resets to "Custom."

---

## 3. Adding Employees

There are 4 ways to enter employee data:

### 3.1 Add Manually

1. Click **＋ Add Employee**.
2. An employee card appears — fill in each field:

| Field | Description | Required |
|-------|-------------|----------|
| Full Name | Employee's legal name | ✅ |
| Nickname | Common name | Optional |
| Team / Note | Select from dropdown (BM1, DW1, etc.) | ✅ |
| Check-in Time | Time arrived at work | ✅ |
| Check-out Time | Time left work | ✅ |
| OT Before (hrs) | Overtime hours before shift | Optional |
| OT After (hrs) | Overtime hours after shift | Optional |
| Leave (hrs) | Hours of leave during shift | Optional |
| Damage Deduct (฿) | Deduction for damaged goods | Optional |
| Remarks | Free-text notes | Optional |

### 3.2 Paste from Excel

1. Click **📋 Paste Excel** (or the 📋 button in the header).
2. A modal appears with a text area.
3. In your Excel/Google Sheets, **select and copy** the attendance rows.
4. **Paste** them into the text area.
5. Click **✅ Import Data**.

**Expected format (tab-separated):**

```
Seq | Full Name | Check-in | Check-out | Team | Nickname
```

> **Tip:** The first column (sequence number) is auto-detected and skipped if present.

### 3.3 Import Excel File (.xlsx / .xls)

1. Click **📥 Import Excel** in the action bar.
2. A file picker dialog opens — select your `.xlsx` or `.xls` file.
3. The tool automatically reads the first sheet and imports employee data.

**Expected column order:**

```
Seq | Full Name | Check-in | Check-out | Team | Nickname
```

> **Auto-detection:** The tool automatically detects and skips header rows (looking for "ลำดับ", "#", "No", "Seq", "STT"). Excel time values (serial numbers) are automatically converted to HH:MM format.

### 3.4 Load Sample Data

Click **🔽 Load Sample Data** on the empty state screen to load 10 example employees. This is useful for testing.

---

## 4. Using Time Chips (Quick Entry)

When you click on the **Check-in** or **Check-out** field, colored **time chips** appear below:

### Check-in Chips

| Chip Color | Meaning |
|------------|---------|
| ⬜ White | ✅ On Time (exact shift start) |
| 🟡 Amber | Late (+5m, +10m, +15m, +20m, +30m...) |

### Check-out Chips

| Chip Color | Meaning |
|------------|---------|
| 🔴 Red | Early departure (−60m, −30m, −15m) |
| ⬜ White | ✅ On Time (exact shift end) |
| 🟢 Green | Overtime (+30m, +60m, +90m, +120m) |

Click a chip to instantly fill the time.

---

## 5. Understanding Live Preview

Each employee card shows a **colored badge** in the header:

| Badge Color | Meaning |
|-------------|---------|
| 🟢 Green | On time — shows "On Time ฿500" |
| 🟡 Amber | Late — shows "Late 15min → ฿484.38" |
| 🔴 Red | Early departure — shows reduced total |
| ⬜ Gray | Intern — shows "฿100" |

The preview updates in **real-time** as you change any field.

---

## 6. Calculating Payroll

### 6.1 Run Calculation

- Click **⚡ Calculate** in the action bar, or
- Press the **⚡ floating button** (bottom-right corner).

### 6.2 Results Table

Switch to the **📊 Results** tab to see:

| Column | Description |
|--------|-------------|
| # | Row number |
| Name | Full name + nickname |
| Team | BM1, DW1, etc. with colored badge |
| Base ฿ | Base wage (500/400/350/100) |
| Late (m) | Deducted late minutes |
| Late Ded. | Lateness deduction in ฿ |
| Early Dep. | Early departure deduction in ฿ |
| OT Pay | Overtime payment in ฿ |
| Leave Ded. | Leave deduction in ฿ |
| Damage | Damaged goods deduction in ฿ |
| **Total ฿** | **Final wage** |

The footer row shows **BM total**, **DW total**, and **Grand Total**.

---

## 7. Summary Tab

The **📋 Summary** tab shows:

- **Stat Cards:** Employee count, total wages, BM total, DW total
- **Alert Cards:** Late arrivals count, early departures count, negative wages count
- **Team Breakdown Table:** Wages grouped by team
- **Calculation Rules:** Active business rules being applied

---

## 8. Exporting Data

There are 3 ways to export results:

### 8.1 Export Excel (.xlsx) ⭐ Recommended

1. Click **📤 Excel** in the Results tab (or the 📤 button in the header).
2. A file named `payroll_YYYY-MM-DD.xlsx` downloads automatically.
3. Opens directly in Excel with proper column widths and formatting.

> **Tip:** Excel export is recommended over CSV because it preserves number formatting and avoids character encoding issues.

### 8.2 Download CSV

1. Click **⬇ CSV** in the Results tab.
2. A file named `payroll_YYYY-MM-DD.csv` downloads automatically.
3. Open in Excel or Google Sheets for further processing.

### 8.3 Print

1. Click **🖨 Print** in the Results tab.
2. The browser print dialog opens with a clean, print-optimized layout.

---

## 9. Bulk Actions

When employees are loaded, a **bulk actions bar** appears:

| Action | Description |
|--------|-------------|
| ✓ Set All Check-out | Sets checkout time for ALL employees to the shift end time |
| ▲ Collapse All | Collapses all employee cards to show only headers |

---

## 10. Switching Language

Click the language buttons in the header:

| Button | Language |
|--------|----------|
| **TH** | Thai 🇹🇭 |
| **EN** | English 🇬🇧 |
| **VI** | Vietnamese 🇻🇳 |

All labels, buttons, calculation rules, and date formatting update instantly.

---

## 📌 Calculation Rules Reference

### Base Wages

| Condition | BM Teams | DW Teams | Interns |
|-----------|----------|----------|---------|
| Full shift (>6 hrs) | ฿500 | ฿500 | ฿100 (flat) |
| Short shift (≤6 hrs) | ฿400 | ฿350 | ฿100 (flat) |

### Standard Hours (Q)

- Full shift (>6 hrs): **Q = 8**
- Short shift (≤6 hrs): **Q = 5**

### Lateness Rules

| Team | Rule |
|------|------|
| **BM** | Rounds UP to nearest 15 minutes (even 1 min late = 15 min deduction) |
| **DW** | Exact minutes (22 min late = 22 min deduction) |
| **Intern** | No deduction |

### Overtime Rate

```
OT Pay = (Base Wage ÷ Q) × 1.5 × OT Hours
```

### Final Wage Formula

```
Total = Base Wage − Late Deduction − Early Deduction + OT Pay − Leave Deduction − Damage Deduction
```

> ⚠️ The total can be negative if deductions exceed the base wage.

---

## ❓ Troubleshooting

| Problem | Solution |
|---------|----------|
| Login doesn't work | Check username/password are exact (case-sensitive) |
| Excel paste imports 0 employees | Make sure data is tab-separated, not comma-separated |
| Time chips don't appear | Click directly inside the time input field |
| Results show ฿0 for everyone | Make sure Check-in and Team fields are filled |
| CSV file shows garbled characters | Open with UTF-8 encoding in Excel |

---

## 🔒 Security Notes

- Login session is stored in **sessionStorage** (cleared when browser tab closes).
- Onboarding skip preference is stored in **localStorage** (persists across sessions).
- No data is sent to any server — **everything runs locally in your browser**.
- Close the browser tab to fully log out.

---

*End of User Guide · Session Staff Payroll Tool v1.0*
