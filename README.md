# Ekhishini Live Stokvel Dashboard

A production-ready Plotly Dash dashboard connected to the Excel workbook in this GitHub repository.

## What the dashboard includes

- Automatic refresh from the GitHub-hosted Excel file
- Local spreadsheet fallback when GitHub is temporarily unavailable
- Current fund value, member contributions, income/profit, active member count and top contributor
- Monthly net movement and cumulative fund chart
- Member-total ranking chart
- Monthly contribution heatmap
- Searchable and sortable detailed table
- CSV download
- Optional password protection
- Render health endpoint at `/health`

## Run locally

```bash
python -m venv .venv
# Windows
.venv\Scripts\activate
# macOS/Linux
source .venv/bin/activate

pip install -r requirements.txt
python DASH.py
```

Open `http://127.0.0.1:8050`.

## Deploy on Render

### Blueprint method

1. Replace or add these files in the root of the GitHub repository.
2. Commit and push them to the `main` branch.
3. In Render, open **Blueprints** and select **New Blueprint Instance**.
4. Connect `leko94/ARISE_DASH_DASHBOARD`.
5. Render reads `render.yaml`, builds the application and assigns an `onrender.com` URL.
6. Enter values for `DASHBOARD_USERNAME` and `DASHBOARD_PASSWORD` when prompted. Leave both empty only when the financial dashboard may be public.

### Manual Web Service method

- Runtime: `Python 3`
- Build command: `pip install -r requirements.txt`
- Start command: `gunicorn DASH:server --workers 2 --threads 4 --timeout 120`
- Health check path: `/health`

## Environment variables

| Variable | Purpose | Default |
|---|---|---|
| `DATA_URL` | Live Excel file URL | GitHub raw URL for `Stokvel_20260719.xlsx` |
| `EXCEL_FILE` | Local fallback spreadsheet | `Stokvel_20260719.xlsx` |
| `SHEET_NAME` | Excel worksheet | `Sheet1` |
| `REFRESH_SECONDS` | Refresh interval; minimum 15 seconds | `60` |
| `DASHBOARD_USERNAME` | Optional basic-auth username | Empty |
| `DASHBOARD_PASSWORD` | Optional basic-auth password | Empty |
| `REQUEST_TIMEOUT` | GitHub request timeout in seconds | `20` |

## Updating the live data

Edit `Stokvel_20260719.xlsx` in the repository and commit the change. The running dashboard requests the GitHub raw file every 60 seconds and recalculates each row's total from the monthly columns. To use a differently named workbook, change `DATA_URL` and `EXCEL_FILE` in Render.

The first column should contain member names. Monthly columns should be named January through December. A row named `Profit`, `Interest`, `Income`, `Dividend`, or `Other Income` is classified as income rather than a member.
