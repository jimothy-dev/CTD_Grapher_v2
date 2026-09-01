# CTD Grapher v2

Interactive depth profiles from Sea-Bird CTD `.cnv` files, in one Colab notebook.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/jimothy-dev/CTD_Grapher_v2/blob/main/CTD_Grapher_v2.ipynb)

Upload your `.cnv` files and run one cell. No Excel import, no Google Drive, nothing to configure per instrument.

## How to use

1. Run **Setup** once per session.
2. Run **1 · Survey location and files** — type the survey area, then pick your `.cnv` files when the upload button appears.
3. Run **2 · Draw the graphs**.

To focus on part of the water column, fill in `depth_from_m` and `depth_to_m` beside the Plot cell and run it again. Loading is a separate cell, so changing the depth window never re-prompts for uploads.

Uploaded the wrong file? Run the Load cell again and re-upload. Colab never overwrites — a second upload of `Station_1.cnv` arrives as `Station_1 (1).cnv` — so the notebook strips the ` (n)`, keeps only the newest copy of each station, and prints which files it ignored.

## Try it without your own data

The `example_data/` folder holds five real casts from Colvos Passage and East Passage, Puget Sound. Download them, upload them to the notebook, and you get the full set of graphs.

## What it plots

One graph per variable, every station overlaid, depth inverted on the Y axis, station colours consistent across all graphs.

Temperature · Salinity · Density (sigma-t) · Dissolved Oxygen · Fluorescence · Beam Transmission · Turbidity · pH · PAR · CDOM

**The sensor set is detected per file.** Only variables actually present get a graph, so casts from different instruments work without changing a setting. Units follow the channel that was found — oxygen appears as mL/L, mg/L, µmol/kg or % saturation depending on what the file recorded. If a file has no depth channel, pressure stands in and the axis says so rather than calling decibars metres.

**You choose what gets graphed.** The Load cell lists every channel in your files as a tick box — recognised variables already ticked, everything else offered in case you want it — with an editable axis label beside each. Depth is the Y axis by default; pick something else and the graphs become ordinary scatter plots titled `Salinity vs Temperature`.

**Titles** come from the survey location: `Quartermaster Harbor: Depth vs Temperature`.

**Colours are locked to station order** — the first station is always the same blue, the second the same red, whatever is being plotted. The Load cell prints the colour key so other charts of the same stations can match it. Any number of stations works and every line stays solid.

| File | Legend |
|---|---|
| `Station_1.cnv` | Station 1 |
| `Station_10.cnv` | Station 10 |
| `East_Passage.cnv` | East Passage |

Stations sort naturally, so Station 2 comes before Station 10.

## What you get

Written to `/content/CTD_output/`:

- **`CTD_profiles.html`** — all graphs together, interactive, ~30 KB. Hover for values, zoom, click the legend to hide a station.
- **`single_graphs/*.html`** — one file per variable (`Temperature.html`, `Salinity.html`, …), ~10 KB each, no page heading so they drop cleanly into someone else's layout.
- **`png/*.png`** — 3× scale static copies for slides and print.
- **`CTD_profiles_offline.html`** — everything inlined, several MB, works with no internet. For presenting without wifi.

### Example output

[`examples/Temperature.html`](examples/Temperature.html) is a real depth vs temperature graph produced from `example_data/`.

GitHub renders README files without JavaScript, so an interactive chart cannot be embedded directly on this page — no markdown or HTML trick gets around it. To see it live, either download the file and open it, or turn on GitHub Pages for this repo (**Settings → Pages → Deploy from a branch → `main` / root**), after which it is served at:

```
https://jimothy-dev.github.io/CTD_Grapher_v2/examples/Temperature.html
```

## Sharing a graph

**For a document or slideshow — use the PNG.** Google Docs, Word, Google Slides and PowerPoint cannot display an interactive chart, and no add-on changes that.

**To let someone explore it — send them the HTML file.** They double-click it and it opens in their browser with everything working, nothing to install. Use `single_graphs/Temperature.html` if you want just the one graph rather than all of them.

For a chart embedded in a web page, hand the HTML file to whoever manages the site and ask for an iframe.

## Other instruments

Columns and the data offset are read from each file's own header, so header length and sensor layout never have to be configured. Checked against thirteen `.cnv` files from public archives — different ships, instruments and decades, from 3 rows to 176,000 — all of which parsed and plotted.

Recognised channels are graphed automatically; anything else in a file is offered as a tick box, so nothing is out of reach.

One caveat: raw casts that have not been through Sea-Bird's `loopedit` still contain the upcast, so profiles double back on themselves. The notebook warns when a file's header shows no `loopedit`.

## Credit

Example data in `example_data/` collected by students of **TGEOS 445, Estuarine Field Studies, University of Washington Tacoma**, Spring 2026, in Colvos Passage and East Passage, Puget Sound.

Instrument: **Sea-Bird SBE 19plus** (temperature and conductivity SN 7686), processed with Sea-Bird SBEDataProcessing.
