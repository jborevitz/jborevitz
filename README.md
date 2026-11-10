# Precision Landscape Regeneration
by Borevitz (ideas,team,papers) ClaudeScience-Opus5 (re-prose and python) Nov8, 2026

Tools for reading a landscape at the resolution it actually varies at, and for
deciding what to do about it.

I'm Justin Borevitz at the Australian National University. These repositories
are the farm-scale end of a larger idea: that the same light-driven programs
studied in a 3 cm Arabidopsis rosette operate, through conserved pathways, all
the way up to the land surface we can see from orbit.

## The idea, briefly

Plant biology spans two nested jumps of roughly a million length units each, or
a trillion in area. A tree holds on the order of **a trillion plant cells**. The
Earth's ice-free surface holds on the order of **a trillion "planet cells"** —
10 m satellite pixels, each one a patch of crop, grass, shrub or tree responding
to its own light, water, nutrients, genetics and management.

The analogy is not decorative. A plant cell type is defined from the bottom up
by its transcriptomic signature; a planet cell type can be defined from the
bottom up by its **spectral-temporal signature** — ten Sentinel-2 bands, weekly,
for a decade. Both are phenotypes of a developmental program running in a local
environment. Annual crops *pulse*, perennial grasses are *dynamic*, evergreens
*persist*, deciduous trees *switch*.

Resolution has made this tractable: 30 m with Landsat-7 in 2000, 10 m with
Sentinel-2 in 2017, and now near-daily commercial imagery under 4 m. As pixels
approach the size of a single tree crown, Earth observation starts to look like
individual-level phenotyping.

Why it matters: humans manage about half the ice-free land surface, so land
accounting that averages a woodland into one "ecosystem type" buries both the
losses and the gains. Granular, pixel-level accounting can tell the difference
between a paddock that should stay in production and a gully that should be
trees — and can support honest counterfactuals, additionality tests and
origin-based trade measures.

This framing is set out in **"Scaling Plant Photomorphogenesis to the Globe"**
(Borevitz, Brown, Aisbett, Bradley and Burley), written in honour of the late
Joanne Chory, whose Harnessing Plants Initiative pursued exactly this scaling —
from an Arabidopsis light-response mutant to deeper, tougher roots holding
carbon in soil.

## The worked example

Everything here is developed against one 46.3 ha mountain property at
**845 Avonside, Snowy Monaro, NSW** — 888 to 1197 m, mapped at **3 m** from
airborne LiDAR. It is deliberately the hardest case: a block as tall as it is
wide, 309 m of relief, and rainfall of 537 mm/yr against a reference
evapotranspiration of 1043 — a deficit near 506 mm every year.

At that resolution the averages stop being useful. Potential ET spans
**318 to 1103 mm/yr across the one property**, and on slopes over 20 degrees
north-facing ground demands 1071 mm/yr against 599 for south-facing: a 472 mm
gap, 88% of the annual rainfall, inside one fence line. The block divides into
four parts on its own terrain, and 365 mm/yr separates the thirstiest from the
least thirsty.

## The packages

Six small single-purpose packages, all AGPL-3.0, each README leading with what it
found and linking its methods. They form a chain, and each link is usable alone.

| package | reads | gives you |
|---|---|---|
| **[pylidarterrain](https://github.com/jborevitz/pylidarterrain)** | classified airborne LiDAR | the ground surface a plant actually stands on: slope, aspect, horizon angles, sky view |
| **[pyterrainsol](https://github.com/jborevitz/pyterrainsol)** | that surface + daily weather | light, PAR, daily light integral and evaporative demand, cell by cell, day by day |
| **[pyterrainwind](https://github.com/jborevitz/pyterrainwind)** | that surface + reanalysis wind | wind exposure by two schemes, and the extra demand it carries |
| **[pychannelform](https://github.com/jborevitz/pychannelform)** | that surface | where the water goes: catchments, channel networks, long profiles |
| **[pylandstore](https://github.com/jborevitz/pylandstore)** | channels + demand | where to hold water, evaporative loss, weir siting, pumping energy |
| **[pylandgraze](https://github.com/jborevitz/pylandgraze)** | all of the above + a herd | terrain-driven grazing allocation: which ground, at which hour, in which season (concept stage) |

Light and wind are separate packages on purpose. Aspect-driven radiation is
predictable geometry and matters at any site with relief; wind needs a terrain-to-
speed transfer function that is scheme-dependent and only partly constrained, so it
is an extra position to take rather than a missing feature. The dependency runs one
way — wind needs the light chain, the light chain never needs wind — so a
collaborator modelling light installs `pyterrainsol` alone and never has to argue
with the wind question.

Still to come: change detection between LiDAR epochs, and a daily water balance
aligned with collaborators' models rather than duplicating them.

## Related work

- **[DAESim](https://github.com/NortonAlex/DAESim)** — the dynamic
  agro-ecosystem simulator this framework uses as its point model
  (Taghikhah, Borevitz, Costanza & Voinov 2022).
- **[TerraSim2](https://github.com/thestochasticman/TerraSim2)** — gridded daily
  soil water on a terrain grid: buckets with an infiltration cap, downslope
  routing and Darcy subsurface drainage, validated against field logger sites.
  The per-cell evaporative demand computed here is what drives it.
- **[PaddockTS](https://github.com/johnburley3000/PaddockTS)** — paddock-scale
  satellite time series.
- **[shelterbelts](https://github.com/ChristopherBradley/shelterbelts)** —
  mapping and quantifying windbreaks.

## Notes

Working notes, open questions and the hard-won traps are kept in
[NOTES.md](NOTES.md), and updated as the work goes.

---

- 👋 Hi, I'm @jborevitz
- 👀 I'm interested in Precision Landscape Regeneration tools
- 🌱 I'm currently learning (meta)genomics and landscape phenomics
- 💞️ I'm looking to collaborate on bioinformatics, remote sensing, and agro-ecosystem modeling
- 📫 How to reach me justin.borevitz@anu.edu.au
