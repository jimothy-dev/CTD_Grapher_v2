# CTD Grapher v2

Interactive depth profiles from Sea-Bird CTD `.cnv` files, in one Colab notebook.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/jimothy-dev/CTD_Grapher_v2/blob/main/CTD_Grapher_v2.ipynb)

Upload your `.cnv` files and run one cell. No Excel import, no Google Drive, nothing to configure per instrument.

**[▶ See an example — Depth vs Temperature, live and interactive](https://jimothy-dev.github.io/CTD_Grapher_v2/examples/Temperature.html)**

Five real casts from Colvos Passage and East Passage, Puget Sound. Hover for values, zoom, click a station in the legend to hide it. The same data is in `example_data/` if you want to run it yourself.

## How to use

1. Run **Setup** once per session.
2. Run **1 · Survey location and files** — type the survey area, pick your `.cnv` files, then tick which variables to graph.
3. Run **2 · Draw the graphs**.

To focus on part of the water column, fill in `depth_from_m` and `depth_to_m` beside the Plot cell and run it again. Loading is a separate cell, so changing the depth window never re-prompts for uploads.

Uploaded the wrong file? Run the Load cell again and re-upload. Colab never overwrites — a second upload of `Station_1.cnv` arrives as `Station_1 (1).cnv` — so the notebook strips the ` (n)`, keeps only the newest copy of each station, and prints which files it ignored.

## What it plots

One graph per variable, every station overlaid, depth inverted on the Y axis:

Temperature · Salinity · Density (sigma-t) · Dissolved Oxygen · Fluorescence · Beam Transmission · Turbidity · pH · PAR · CDOM

Only variables a file actually contains get a graph, with units taken from the channel found — so oxygen may read mL/L, mg/L, µmol/kg or % saturation. Pressure stands in where there is no depth channel, labelled as pressure.

The Load cell lists every channel as a tick box, recognised ones already ticked, each with an editable axis label. Depth is the Y axis by default; choose another and the graphs become scatter plots titled `Salinity vs Temperature`. A tick box below inverts the axis, on by default.

Titles come from the survey location: `Quartermaster Harbor: Depth vs Temperature`. Filenames become legend labels — `Station_1.cnv` → **Station 1**, `East_Passage.cnv` → **East Passage** — sorted naturally, so Station 2 precedes Station 10.

Colours are locked to station order and printed as a key, so other charts of the same stations can match them.

## What you get

Written to `/content/CTD_output/`:

- **`CTD_profiles.html`** — all graphs together, interactive, ~30 KB. Hover for values, zoom, click the legend to hide a station.
- **`single_graphs/*.html`** — one file per variable (`Temperature.html`, `Salinity.html`, …), ~10 KB each, no page heading so they drop cleanly into someone else's layout.
- **`png/*.png`** — 3× scale static copies for slides and print.
- **`CTD_profiles_offline.html`** — everything inlined, several MB, works with no internet. For presenting without wifi.

## Sharing a graph

**For a document or slideshow — use the PNG.** Google Docs, Word, Google Slides and PowerPoint cannot display an interactive chart, and no add-on changes that.

**To let someone explore it — send them the HTML file.** They double-click it and it opens in their browser with everything working, nothing to install. Use `single_graphs/Temperature.html` if you want just the one graph rather than all of them.

For a chart embedded in a web page, hand the HTML file to whoever manages the site and ask for an iframe.

## Other instruments

Columns and the data offset are read from each file's own header, so header length and sensor layout never have to be configured. Checked against thirteen `.cnv` files from public archives — different ships, instruments and decades, from 3 rows to 176,000 — all of which parsed and plotted.

Recognised channels are graphed automatically; anything else in a file is offered as a tick box, so nothing is out of reach.

## Raw casts

A cast that has not been processed still contains the upcast, so the profile doubles back on itself. With **downcast_only_raw** ticked, the Load cell keeps the downcast only. Files that were already processed are left alone.

**That is a display fix, not processing.** It makes the shape readable; it does not correct the values. Sea-Bird's software does several correction steps before salinity is calculated, and this notebook does none of them — it cannot, from a `.cnv` alone. The Load cell says so when a file looks uncorrected.

If the numbers matter, process the casts properly first — with Sea-Bird's own software or [HakaiInstitute/seabird-processing](https://github.com/HakaiInstitute/seabird-processing) — then plot the results here.

## Credit

Example data in `example_data/` collected by students of **TGEOS 445, Estuarine Field Studies, University of Washington Tacoma**, Spring 2026, in Colvos Passage and East Passage, Puget Sound.

Instrument: **Sea-Bird SBE 19plus** (temperature and conductivity SN 7686), processed with Sea-Bird SBEDataProcessing.

## Licence

[GNU General Public License v3.0](LICENSE). Free to use, change and share, including commercially. Anything you distribute that is built on it must stay under the same licence and ship its source.

Copyright (c) 2026 James Simpson

The licence covers the code in this repository. Example data and any course documents remain the property of their authors and are included with credit, not relicensed.
