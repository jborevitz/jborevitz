# Avonside — notes and to-do

Precision Landscape Regeneration, worked at 845 Avonside Benboulder (46.3 ha,
888–1197 m, Snowy Monaro NSW). Last updated 8 November 2026.

Code lives in six MIT-licensed repos under [github.com/jborevitz](https://github.com/jborevitz):
`pylidarterrain`, `pyterrainsol`, `pychannelform`, `pylandstore`. Each README
leads with findings and links its methods page; results and methods are kept
apart on purpose.

---

## Next up

**The 2025 daily ET movie at 3 m, with wind.** Reference ET for every day of
2025 on the 3 m grid, with the aerodynamic term switched on, rendered as a
365-frame mp4 with wind speed and direction shown alongside. The point is to see
the spatial and temporal variation *together* — a farm 300 m tall and four parts
wide cannot be represented by a centroid, and a map cannot show the seasonal
reversal. Every input is already in place: the Sx field, BARRA at 20 minutes,
SILO daily, and the animation pipeline from the solar and ET movies.

---

## Open work

### Modelling
- **Branch the demand model** so a user picks Priestley-Taylor (no wind) or
  Penman-Monteith (wind × VPD). SILO supplies the humidity; mind the VPD trap
  below.
- **Calibrate Sx to a wind multiplier.** This is the missing physical link —
  Sx is geometry, and nothing converts degrees of arc to m/s without
  measurement. Cheapest honest route: two anemometers, one on the west ridge of
  the top farm (Sx −1.7°) and one in the upper forest (+25.6°), and a measured
  ratio. The Sx map says exactly where to put them to span the range.
- **Test lee spill against channelling on real days** rather than arguing from
  maps: if spill is operating, gale hours should carry a warm, dry anomaly
  relative to the upstream air. BARRA temperature and SILO humidity can settle
  it.
- **Daily water balance** — the per-cell PET field drops into TerraSim2's
  `pet` argument unchanged, since it is already a `(rows, cols)` ndarray.

### Terrain
- **Eastern crest boundary** — the fitted line carries an 87 m rms residual
  because the ridge is sharp at both ends and broad in the middle. Deferred by
  decision: revisit alongside a close look at the TWI map, not as a standalone
  boundary fit.
- **Canopy height model** — deferred until it can be an *editable management
  layer* rather than a fixed 2018 snapshot. It would also supply the tree/grass
  mask for per-cell root depth (1 m under trees, 0.3 m under grass).
- **Change detection** — separate the shed excavation from post-construction
  sand movement; package the code as `pydemchange`.
- **Ground control points** for future drone flights. The 2018 LiDAR resolves
  gully *depth* but not *width*; a 1 m gully in a 3 m cell averages to about
  0.3 m apparent, so the drone at ~300 pts/m² is the right instrument.

### Design and management
- **20 m contour steps** as management and hiking trails, spreading water east
  into Granda's orchard area.
- **Fraction of annual rain arriving in events under ~15 mm** — settles whether
  a thick litter layer is a net interception loss in summer. Now computable
  from SILO daily rain.
- **Management-layer switches**: litter (low-density-thin now, high-density-
  thick later) and soil structure (gypsum/lime to aggregate; overgrazing and
  tilling to destroy).

### People and admin
- Tell **Yasar** that TerraSim2 and DownscalingMoistureModel have no LICENSE
  file; three deliverables offered (`step_bucket_routed`, the per-cell PET field
  in `ModelInputs` form, and the TOPMODEL groundwater store) and awaiting his
  pick.
- Tell **John Burley** that the PaddockTS LICENSE is an HTML page.
- **Licence settled: MIT across all repos** (Nov 2026, agreed with Chris Bradley so the
  group's packages can share code in both directions). Two follow-ups: check **ANU IP
  policy**, since university employment usually vests IP in the institution; and note
  that `pylidarterrain` depends on **richdem, which is GPLv3** — an MIT package with a
  GPL runtime dependency is a known grey area, so either make richdem optional or
  replace it.
- **Lab-facing summary document**, and the farm-to-globe microclimate-to-
  macroclimate cartoons.

---

## Notes for whoever comes next, human or AI

Each of these cost real time to find.

**Forcing data**
- **BARRA timestamps are UTC.** Shift to AEST before any diurnal analysis or the
  afternoon wind maximum lands at dawn.
- **SILO's `vp_deficit` is not the FAO-56 VPD.** FAO uses
  *(es(Tmax)+es(Tmin))/2 − ea*, which averages 0.614 kPa here against SILO's
  published 0.836. Feed SILO's column straight into Penman-Monteith and ET0
  comes out 17% high (1219 against 1065 mm/yr).
- **SILO's `et_short_crop` assumes a fixed wind.** Solving for the wind that
  reproduces its 20-year mean gives u2 = 1.93 m/s, essentially the FAO-56
  default of 2. It cannot see a windy day.
- **SILO's grid point sits at 861 m** against a farm mean near 1040 m, so it
  runs warm and its ET high. Lapse-correct at 6.5 °C/km before driving anything
  farm-scale.
- **Never average directions** — average the components, and check
  `directional_constancy` before quoting a mean direction at all.
- **Do not calibrate a wind climatology on one year.** 2025 ranks 1st of 20 on
  both R² and annual amplitude. Fit the pool; use single years for spread.
- **Weibull k is scale-free.** Doubling every speed moves c and leaves k alone,
  so a low k is never an artefact of a low background.

**Terrain code**
- **Affine transforms index cell corners; array interpolation indexes cell
  centres.** Subtract 0.5 or every profile is displaced half a cell.
- **Sx rays must start at the adjacent cell,** not at the step size. Starting at
  `step` changed the Avonside field by up to 21° on gully walls — far more than
  coarsening the step further out. Sx is dominated by the nearest terrain.
- **Terrain shading is not cloud.** Feeding a shaded cell's own low radiation
  into the longwave cloudiness factor treats a hillside as an overcast sky.
  Pass a spatially uniform clearness index instead.
- **richdem's epsilon depression fill needs float64.** In float32 the tiny
  gradient it adds across a flat lake is lost to rounding and every downstream
  trace stalls.
- **Write the common-sense test early.** A synthetic-signal test caught a
  coefficient-ordering bug in `harmonic_summary` that had silently swapped the
  annual and daily cycles, and two separate Sx sampling bugs. Neither was
  visible by eye in a finished figure.

**This sandbox**
- `ProcessPoolExecutor` fails on POSIX semaphores; use N shell-launched worker
  processes with `wait`. Threads are fine.
- netCDF4/HDF5 is not thread-safe over OPeNDAP.
- `nohup ... &` does not outlive a bash cell — use a background cell instead.
- Git pushes emit benign `failed to get/store: -50` keychain noise with exit 0.

---

## Numbers worth not re-deriving

| quantity | value |
|---|---|
| farm | 46.3 ha, 51,467 cells at 3 m, 887.9–1197.0 m AHD |
| rainfall, 2006–2025 | 537 mm/yr (range 320–784) |
| reference ET | 1043 mm/yr (SILO), 1065 computed — deficit ≈ 506 mm |
| ET by part | top farm 899, upper forest 653, northerly woodland 1018, lower forest flats 809 mm/yr |
| Sx by part, 500 m fetch | +5.2, +25.6, +10.5, +19.3° (same order) |
| wind | mean 10.56 km/h, max 70.2; 89% of steps >36 km/h from WNW–NW |
| wind's worth in ET | +15 mm/yr in the mean, −8% to +18% across wind deciles, 61% of demand in midwinter |
| Main Range | 2226 m, 41.5 km away on bearing 268° — subtends 1.6° |
| shelter | all of it comes from within 1 km; beyond 25 km contributes 1.4° |
