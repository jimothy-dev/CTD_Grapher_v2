# CTD Grapher v2

Interactive depth profiles from Sea-Bird CTD `.cnv` files, in one Colab notebook.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/jimothy-dev/CTD_Grapher_v2/blob/main/CTD_Grapher_v2.ipynb)

The user adds their `.cnv` files and runs one cell. No Excel import, no Google Drive, no per-lab setting to choose.

## How to use

1. Run **Setup** once per session.
2. Run **1 · Load** and pick your `.cnv` files with the upload button.
3. Run **2 · Plot**.

To focus on part of the water column, type a depth into the `bottom_m` field beside the Plot cell and run it again — the graphs redraw and the x axis rescales to the values inside that window. Loading is a separate cell so changing the depth window never re-prompts for uploads.

Filenames become legend labels, with underscores turned into spaces:

| File | Legend |
|---|---|
| `Station_1.cnv` | Station 1 |
| `Station_10.cnv` | Station 10 |
| `East_Passage.cnv` | East Passage |
| `Dockton.cnv` | Dockton |

Stations sort naturally, so Station 2 comes before Station 10.

## What it plots

One graph per variable, every station overlaid, depth inverted on the Y axis, station colours consistent across all graphs.

Temperature · Salinity · Density (sigma-t) · Dissolved Oxygen (mL/L) · Fluorescence · Beam Transmission · Turbidity · pH

**The sensor set is detected per file**, so UW-Tacoma casts (beam transmission) and Friday Harbor casts (turbidity + pH) both work with nothing to configure. Only variables actually present in a file get a graph.

## Outputs

Written to `/content/CTD_output/` and refreshed on every update:

- **`CTD_profiles.html`** — every graph in one self-contained file. `plotly.js` is inlined, so it is ~4 MB but works offline on any machine with no install. Hyperlink a slide to it and it opens interactive during a presentation.
- **`png/*.png`** — 3× scale static copies for slides and print.

Google Slides cannot embed HTML at all, and PowerPoint's Web Viewer add-in needs a hosted URL, so a hyperlink to the local HTML is the most reliable way to show the interactive version in a talk.

## Settings

At the top of the **Setup** cell:

| Setting | Default | Notes |
|---|---|---|
| `CNV_FOLDER` | `/content/CTD Files` | Read from here if it has files, otherwise show the upload button. Set to `None` to always upload. |
| `LINE_SHAPE` | `"spline"` | `"linear"` shows the exact 1-db bins with no curve fitting. |
| `SHOW_MARKERS` | `False` | `True` puts a dot at each bin. |
| `X_PAD_FRAC` | `0.05` | Padding at the left/right edges so lines never touch the frame. |
| `EXPORT_PNG` | `True` | Needs `kaleido`, installed by the Setup cell. |

## Format notes

Things that bite when parsing `.cnv`, all handled here:

- **Data starts after `*END*`**, and columns are declared in `# name N = short: Description [units]` header lines. Both are read from the file, so header length is never hardcoded — this is the main fix over v1, which assumed row 560 for UW-T and row 220 for FHL.
- **Headers are cp1252, not UTF-8.** Reading as UTF-8 corrupts or throws on `sigma-θ00`.
- **`bad_flag = -9.990e-29`** marks missing data and becomes `NaN`. The comparison has to be purely relative (`atol=0`), or every near-zero reading gets wiped out.
- **`loopedit` in the header** means the upcast has already been removed. Files without it may still contain it, and the notebook prints a warning so the profile doubling back on itself is not a mystery.

## Colab rendering constraint

Plots are emitted with a plain `fig.show()` and nothing clears the cell. This is deliberate. Colab's plotly renderer attaches a `MutationObserver` that calls `Plotly.purge()` when a figure's output element is rebuilt, so `clear_output()` destroys the figures as they arrive — the notebook looks like it ran fine and shows nothing. An ipywidgets `Output()` is no help either: `fig.show()`, `display(fig)` and `append_display_data()` all come back empty or with no payload inside one. Re-running the Plot cell is the reliable way to redraw.

## Differences from [CTD_Grapher](https://github.com/jimothy-dev/CTD_Grapher)

| v1 | v2 |
|---|---|
| Excel input, manual import step | `.cnv` read directly |
| Hardcoded parse-start row per lab | `*END*` detected per file |
| User picks UW-T or FHL | Sensors auto-detected from header |
| Static PDF to Google Drive | Interactive HTML + PNG, no Drive |
| Fixed depth range | Depth window typed in, re-run to apply |
