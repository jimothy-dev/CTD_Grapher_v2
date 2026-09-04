# CTD Grapher v2

Colab notebook that turns Sea-Bird `.cnv` CTD casts into interactive multi-station depth profiles — upload the files, run one cell, get HTML and PNG graphs.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/jimothy-dev/CTD_Grapher_v2/blob/main/CTD_Grapher_v2.ipynb)

**Web app version** (no Colab needed, same graphs plus a transect tool): https://jimothy-dev.github.io/CTD_Grapher_Web/ — source at [CTD_Grapher_Web](https://github.com/jimothy-dev/CTD_Grapher_Web).

**Live example:** https://jimothy-dev.github.io/CTD_Grapher_v2/examples/Temperature.html — five casts from Colvos Passage and East Passage, Puget Sound. Hover for values, zoom, click a station in the legend to hide it.

This replaces [CTD_Grapher](https://github.com/jimothy-dev/CTD_Grapher) (v1), which needed a manual Excel import and hard-coded column positions for two specific instruments.

## What it does

- Accepts any number of `.cnv` files through Colab's upload dialog; filenames become legend labels (`Station_1.cnv` → **Station 1**), sorted naturally.
- Detects which variables each file contains and plots one graph per variable with all stations overlaid, depth inverted on the Y axis.
- Recognised variables: Temperature, Salinity, Density (sigma-t), Dissolved Oxygen, Fluorescence, Beam Transmission, Turbidity, pH, PAR, CDOM. Every other channel in the file is offered as a tick box.
- Optional depth window (`depth_from_m` / `depth_to_m`) and a custom Y axis, which turns the output into scatter plots such as `Salinity vs Temperature`.
- Writes to `/content/CTD_output/`: `CTD_profiles.html` (all graphs, ~30 KB, loads Plotly from CDN), `single_graphs/*.html` (one per variable), `png/*.png` (3x scale), and `CTD_profiles_offline.html` (Plotly inlined, works without internet).

## How it works

- **Parsing.** `parse_cnv()` reads column names from the `# name` lines in the file header and starts data after the `*END*` marker, so header length is never hard-coded. Headers are decoded as latin-1 because Sea-Bird writes cp1252 (the theta in sigma-theta).
- **Channel mapping.** A `VARIABLES` table maps each canonical variable to the Sea-Bird short names that can carry it, in priority order, with the unit attached to the name — so oxygen may correctly read mL/L, mg/L, µmol/kg or % sat depending on the cast. Depth prefers `depSM`/`depFM`; pressure (`prdM`, `prSM`, `pr`) stands in and is labelled as pressure. Housekeeping channels (`scan`, `flag`, `pumps`, `timeJ`, ...) are never offered.
- **Raw casts.** `downcast_only()` cuts at the deepest reading and keeps only monotonically deeper samples, so an unprocessed cast that includes the upcast stops doubling back. This is a display fix, not Sea-Bird processing, and the notebook says so.
- **Upload hygiene.** Colab never overwrites, so a re-upload arrives as `Station_1 (1).cnv`; the loader strips the suffix, keeps the newest copy per station and prints what it ignored.
- **Plotting.** Plotly `graph_objects` with a fixed station palette (colour is tied to station order and printed as a key so other figures can match). PNGs are exported through `kaleido`.
- Tested against the five example casts plus thirteen public-archive `.cnv` files from different ships, instruments and decades (3 to 176,000 rows).

## Run it

1. Open the notebook in Colab (badge above) and use a standard CPU runtime.
2. Run **Setup** (installs `kaleido==0.2.1`; `plotly`, `pandas`, `numpy`, `ipywidgets` are preinstalled in Colab).
3. Run **1 · Survey location and files** — type the survey area, pick the `.cnv` files, tick the variables to graph. Leave `downcast_only_raw` ticked unless the files are already processed.
4. Run **2 · Draw the graphs**. Change `depth_from_m` / `depth_to_m` and re-run this cell alone to zoom the water column without re-uploading.
5. For a station map and a transect, run **3 · Station positions and transect** (positions, then tick and drag the stations into order along the line, adding any seafloor depths known between them) and **4 · Station map and water column transect**. Not needed for the graphs alone.
6. Download from the folder icon in the sidebar: `/content/CTD_output/`.

To try it without your own data, upload `example_data/Station_11.cnv` ... `Station_17.cnv`.

## Data sources

- `example_data/` — five SBE 19plus casts (temperature/conductivity SN 7686) from Colvos Passage and East Passage, Puget Sound, 15 May 2026, collected by students of TGEOS 445 Estuarine Field Studies, University of Washington Tacoma, processed with Sea-Bird SBEDataProcessing.
- `examples/Temperature.html` — the committed output served by GitHub Pages.

## Limitations / next steps

- No sensor corrections: alignment, cell thermal mass, loop edit and derived salinity are Sea-Bird processing steps that cannot be done from a `.cnv` alone. Process casts first (Sea-Bird software or [HakaiInstitute/seabird-processing](https://github.com/HakaiInstitute/seabird-processing)), then plot here.
- Colab-only for now; a local Jupyter version would replace `google.colab.files.upload()` with a file picker.
- Spline line shape is cosmetic; switch `LINE_SHAPE = "linear"` in Setup to see the raw 1 db bins.

## Author

James Simpson — https://github.com/jimothy-dev

Licence: [GPL-3.0](LICENSE). Example data remains with its authors.
