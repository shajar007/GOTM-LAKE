## What is GOTM?

GOTM - the **G**eneral **O**cean **T**urbulence **M**odel is an ambitious name for a one-dimensional water column model for marine and limnological applications. It is coupled to a choice of traditional as well as state-of-the-art parameterisations for vertical turbulent mixing. The package consists of the FORTRAN source code, a number of idealised and realistic test cases, and a scientific documentation, all published under the GNU public license.

A comprehensive description including compilation instructions are given at the official [GOTM homepage](http://www.gotm.net/portfolio/software).

## The lake branch

This repository is a fork of [gotm-model/code](https://github.com/gotm-model/code), maintained for lake and reservoir applications. Its `lake` branch continues the upstream `lake` branch, which split from GOTM master in November 2019 (`f19be734`) and is no longer developed upstream (last upstream commit `93135603`, August 2025). The upstream master has moved on separately and does not contain the lake features.

What the lake branch adds to GOTM:
- **Hypsography:** layer areas and volumes follow the lake's area-depth curve.
- **Streams:** inflows and withdrawals at given depths, with their temperature, salinity and biogeochemical concentrations.
- **Water balance** (`water_balance_method`): the water level can follow the inflows, outflows, precipitation and evaporation (`3`, free surface) or be corrected by a residual stream (`1`, `2`).
- **FABM with `bottom_everywhere`:** the benthic-pelagic coupling acts in every layer on the part of the lake bed that layer touches, not only in the bottom layer.

### Fixes in this fork
Found while closing nitrogen, phosphorus and silica budgets of Lake Kinneret with FABM-SelmaProtbas (merged October 2026):
1. **Top-layer benthic flux:** with `bottom_everywhere`, the top layer's sediment-water flux was overwritten by the surface flux and lost. It is now kept. *Changes results.*
2. **Mass added by `repair_state`:** printed at the end of the run. A time series is available as `<variable>_repaired`. List these by name in output.yaml (debug level, not included by `source: *`).
3. **Input scale factors:** inputs that read the same file column each got the product of all their scale factors. Each now gets its own `scale_factor` and `offset`. *Changes results where columns are shared.*
4. **Patankar solvers:** `ode_method` 4-11 ignore `bottom_everywhere`, the hypsograph and surface fluxes, so GOTM-lake now stops with a message if one of them is selected.
5. **Water-level changes:** the benthic state is remapped conservatively when the level moves; newly flooded bed takes the content of the top layer. The exchange with the shoreline is output as `<variable>_shoreline`. Nothing changes at a constant level.

### Getting it
```
git clone --recursive -b lake https://github.com/shajar007/GOTM-LAKE.git
```
Compilation is as for GOTM (see the homepage above). FABM is a submodule in `extern/fabm`, or can be given with `-DFABM_BASE=<path>`.

Contact: Shajar Regev, Israel Oceanographic and Limnological Research.
