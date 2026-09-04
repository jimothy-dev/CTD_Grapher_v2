# CTD Grapher v2

Colab notebook that turns Sea-Bird `.cnv` CTD casts into interactive depth profiles, a station map and transect sections — upload the files, run the cells, get HTML and PNG graphs.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/jimothy-dev/CTD_Grapher_v2/blob/main/CTD_Grapher_v2.ipynb)

**Web app version** (no Colab needed, same graphs, waypoints on the map): https://jimothy-dev.github.io/CTD_Grapher_Web/ — source at [CTD_Grapher_Web](https://github.com/jimothy-dev/CTD_Grapher_Web).

**Live example:** https://jimothy-dev.github.io/CTD_Grapher_v2/examples/Temperature.html — five casts from Colvos Passage and East Passage, Puget Sound.

This replaces [CTD_Grapher](https://github.com/jimothy-dev/CTD_Grapher) (v1), which needed a manual Excel import and hard-coded column positions for two specific instruments.

## What it does

- Accepts any number of `.cnv` files through Colab's upload dialog; filenames become legend labels (`Station_1.cnv` → **Station 1**), sorted naturally.
- Detects which variables each file contains and plots one graph per variable with all stations overlaid, depth inverted on the Y axis; any variable can go on Y instead, and extra graphs pair any two variables.
- Recognised variables: Temperature, Salinity, Density (sigma-t), Dissolved Oxygen, Fluorescence, Beam Transmission, Turbidity, pH, PAR, CDOM. Every other channel in the file is offered as a tick box.
- Station positions from the header or typed in any format; a station map; transect sections with stations dragged into order, seafloor points between, before and beyond them, fixed colour ranges per variable, and your own palette file.
- Writes to `/content/CTD_output/`: interactive HTML (all graphs, and one file per graph), PNGs, and an offline HTML copy with Plotly inlined.

## How it works

- **Parsing.** `parse_cnv()` reads column names from the `# name` lines in the header and starts data after `*END*`, so header length is never hard-coded. Headers are decoded as latin-1 because Sea-Bird writes cp1252 (the theta in sigma-theta). Fields that run together are recovered from Sea-Bird's 11-character columns.
- **Channel mapping.** A `VARIABLES` table maps each canonical variable to the Sea-Bird short names that can carry it, in priority order, with the unit attached to the name — so oxygen may correctly read mL/L, mg/L, µmol/kg or % sat depending on the cast. Depth prefers `depSM`/`depFM`; pressure stands in and is labelled as pressure.
- **Raw casts.** `downcast_only()` keeps the downward half of an unprocessed cast so it stops doubling back. A display fix, not Sea-Bird processing; moored time series are left alone.
- **Plotting.** Plotly with a station palette tied to station order (printed as a key so other figures can match), cmocean colour maps for sections, PNGs through `kaleido`.

## Run it

1. Open the notebook in Colab (badge above) and use a standard CPU runtime.
2. Run **Setup** (installs `kaleido==0.2.1`; `plotly`, `pandas`, `numpy`, `ipywidgets` are preinstalled in Colab).
3. Run **1 · Survey location and files** — pick the `.cnv` files, tick the variables to graph.
4. Run **2 · Draw the graphs**. Change the depth window or the graph options and re-run this cell alone.
5. For a station map and a transect, run **3 · Station positions and transect** (positions, then tick and drag the stations into order along the line, adding any seafloor depths known between them) and **4 · Station map and water column transect**.
6. Download from the folder icon in the sidebar: `/content/CTD_output/`.

To try it without your own data, upload `example_data/Station_11.cnv` ... `Station_17.cnv`; their positions fill in by themselves.

## Known limits

- No sensor corrections: alignment, cell thermal mass, loop edit and derived salinity are Sea-Bird processing steps that cannot be done from a `.cnv` alone. Process casts first (Sea-Bird software or [HakaiInstitute/seabird-processing](https://github.com/HakaiInstitute/seabird-processing)), then plot here.
- On a section everything between stations is interpolated, and the seafloor is the casts' deepest readings plus the points you add, not surveyed bathymetry.
- Fixed colour ranges are chosen for Puget Sound and temperate estuaries; change `VARIABLE_RANGES` in Setup for other waters.
- Colab-only; a local Jupyter version would replace `google.colab.files.upload()` with a file picker. The web app has no such limit.

## Data sources

- `example_data/` — five SBE 19plus casts (temperature/conductivity SN 7686) from Colvos Passage and East Passage, Puget Sound, 15 May 2026, collected by students of TGEOS 445 Estuarine Field Studies, University of Washington Tacoma, processed with Sea-Bird SBEDataProcessing. Positions in `example_data/positions.txt`.
- `examples/Temperature.html` — the committed output served by GitHub Pages.

## Cite

Simpson, J. (2026). CTD Grapher v2 (Colab notebook). https://github.com/jimothy-dev/CTD_Grapher_v2 (see `CITATION.cff`).

Licence: [GPL-3.0](LICENSE). Example data remains with its authors.
