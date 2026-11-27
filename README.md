# PSA × Blood Test Correlation Analyzer for Prostate Cancer Patients

A single-page web tool: upload a **lab results Excel file** and a **diagnosis dates Excel file**, and it automatically computes how **PSA correlates with blood sugar, lipids, red/white blood cells and other blood tests**, with interactive statistical charts.

🔒 **Privacy**: all files are parsed and analyzed only in the user's own browser — **nothing is uploaded to any server**.

> 中文版 / Chinese version: https://hanhan-wq131.github.io/psa-lab-correlation/

## How to use

1. Open the page (GitHub Pages link). For offline use, download the **whole folder** (`index.html` must stay next to the `vendor/` folder) and open `index.html` in Chrome or Edge — no internet connection needed.
2. Upload the **lab results** Excel file.
3. Upload the **diagnosis dates** Excel file.
4. Results appear automatically; change any setting and they update instantly.

## File format

**Lab results file** (columns are detected automatically by name and can be remapped manually)

| Field | Default column | Notes |
|---|---|---|
| Patient ID | C | Must match the diagnosis file |
| Specimen | G | **B = blood (analyzed)**; non-blood specimens (e.g. U = urine) are excluded |
| Test name | I | e.g. PSA, Glucose(AC), WBC, T-Cholesterol |
| Date | J | YYYYMMDD, Excel dates and Taiwanese (ROC) dates are supported |
| Result | L | `<0.008`-style values use the detection limit; eGFR `>60(64)` uses the value in brackets; text results (e.g. Negative) are skipped |
| Unit | T | Optional |

**Diagnosis dates file**: needs a patient ID and the first prostate cancer diagnosis date. If the workbook has several sheets, the one with a diagnosis-date column is chosen automatically (you can switch).

Both Chinese and English column headers are recognized.

## What it produces

- **Correlation bar chart**: Spearman ρ between PSA and every blood test, filterable by category (glucose, lipids, blood cells, liver, kidney, electrolytes, hormones…).
- **Results table**: pairs, patients, Spearman ρ, p-value, FDR-corrected q-value and log(PSA) Pearson r — downloadable as CSV.
- **Scatter plot**: PSA (log scale) vs. the selected test, with a trend line.
- **Box plot by PSA group**: < 4, 4–10, 10–20, ≥ 20 ng/mL.
- **Timeline relative to diagnosis**: median and IQR in 3-month bins, with diagnosis at 0.
- **Patient view**: PSA and the selected test over time for one patient.
- **Automatic summary**: plain-language list of significant positive/negative correlations and caveats.

## Methods

- **Pairing**: each PSA result is paired with the same patient's nearest result of another test within ±N days (default 7).
- **Analysis period**: all time, before diagnosis, after diagnosis, ±1 year, or a custom range (based on the PSA test date relative to diagnosis).
- **Unit of analysis**: "Every pair" uses all pairs; "One point per patient" takes each patient's median to avoid bias from repeated measurements.
- **Statistics**: Spearman rank correlation (primary; robust to PSA's skewed distribution) and Pearson correlation on log10(PSA); p-values from the t-distribution; Benjamini–Hochberg FDR correction for multiple comparisons.

## Caveats

For exploratory research only — correlation does not imply causation. PSA and some tests (e.g. testosterone) are affected by treatment such as hormone therapy; interpret results in clinical context.

## Technology

A single static HTML file using [SheetJS](https://sheetjs.com/) (xlsx 0.18.5) to read Excel and [Plotly.js](https://plotly.com/javascript/) (2.35.2) for charts. Both libraries are bundled in `vendor/`, so no external CDN, installation or server is required.
