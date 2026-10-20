# Airport Time / Space

A standalone JavaScript lab that reconstructs free 3D point positions from
pairwise distances. The optimizer uses no airport geography, assumed sphere,
or rendered surface. The surface follows the current point cloud for display.

## Run and check

From this directory, with Node.js and Python 3 installed:

```sh
npm start
npm run check
npm test
npm run benchmark
npm run benchmark:plane
```

No npm installation is required for this app. `dist/` contains the complete
static website, including all flight observations, flags, examples, and reports.
The Python collection and validation scripts use the standard library.

The published app remains at <https://yadan.net/lab/airport-time-space/>.
The [website repository](https://github.com/omry/yadan.net) downloads a pinned,
checksum-verified release archive into its generated static files. App source,
datasets, and reports are maintained here.

## Releases

Push a `v*` tag to run the checks and publish `airport-time-space.tar.gz` as a
GitHub release asset. The archive contains the complete `dist/` website.

To deploy an app update, update the release URL and SHA-256 in
`lab/airport-time-space.release.json` in the website repository, then deploy
that website. Get the archive checksum with `sha256sum airport-time-space.tar.gz`.
Existing website deployments keep using their pinned app release.

## Experiments and fitting

All points start at seeded random XYZ positions (or an optional flat start).
Degree-normalized gradient descent with backtracking reduces weighted squared
distance error. Annealing perturbs groups and tries another random start to
escape local minima while preserving the best visible fit. A flat start can
leave its plane using a negative-curvature direction from the same loss.

The synthetic sphere and flat-plane datasets use exact Euclidean distances;
reference positions are withheld from the optimizer. Flight schedules are only
a noisy distance proxy: winds, routing, aircraft speeds, and airport overhead
can distort the inferred shape. Neither low error nor roundness proves that
real geography has been recovered. Rotation and reflection are arbitrary.

Defaults use recognized jets, at least four connections per airport, repeated
triangle-inconsistency filtering, and reduced weight for flights under three
hours. No uniform overhead is subtracted. Directional observations are averaged
separately, then both directional means receive equal weight when available.
Text labels show the top 10% hubs; all retained airports take part in fitting.
Selecting an airport in the list centers and reveals its marker and label.

## CSV import

Required columns: `from_airport,to_airport,minutes`.
Optional columns: `aircraft,aircraft_code`. Durations must be positive and at
most 2,880 minutes. Use 4–300 connected airports, at most 100,000 observations,
and a file no larger than 32 MB. Aircraft data enables jet filtering. Uploaded
records are assumed to be nonstop; they are not independently verified.
Geographic and other extra columns are ignored. CSV processing stays in the
browser. The app's **CSV format** help includes a downloadable complete example.

## Data and provenance

Built-in data contains dated, aircraft-specific published nonstop schedules,
not actual flown times. `flights.json` lists the observation shards and each
record retains its original schedule source and nonstop-verification evidence.
`aircraft-flights.csv` exports the observations. Audits and earlier investigation
reports are available under About → Data & downloads; reports describe their
own historical filter settings and are not necessarily the current default fit.

Collectors, validation scripts, benchmarks, and existing regression tests are
retained in `scripts/`. Independent reference geography used in earlier
investigations is not bundled or supplied to the production optimizer.
FlagCDN flags are bundled as data URIs. The flight-route evidence license is
preserved in `scripts/flight-route-evidence-LICENSE.txt`. Google Fonts is the
only optional runtime network dependency; system fonts provide a fallback.

This import preserves the deployed app from source commit
`bf727d703e58716a4ae572213d7139fb195101e9`; Sites project metadata and credentials
are not part of the repository.

This repository was extracted from `lab/airport-time-space/` in
`omry/yadan.net`, originally imported there in commit
`c9a834023fe5704ff7017dc41e6fabc11a9901ea`.

## Dependency maintenance

Use Node 24 and npm 11.9.0, matching the project declarations. Install the
pinned npm version with `npm install --global npm@11.9.0`, then use `npm ci`.

The [Node maintenance workflows](https://github.com/hydra-ecosystem/github-actions/blob/main/docs/node-maintenance.md)
check security every Monday at 02:00 UTC and propose routine upgrades quarterly,
on January 1, April 1, July 1, and October 1 at 02:00 UTC. New releases have a
ten-day cooldown. The local `.github/node-maintenance.json` points to the shared
policy; edit its overrides to change project policy, and keep caller cron entries
in `.github/workflows/dependency-audit.yml` consistent with the resolved schedule.

To run an audit sooner, open **Actions → Node dependency maintenance → Run workflow**
on `main` and select `security` or `upgrades`. The action validates dependency
changes before opening or updating one maintenance PR. A toolchain or schedule
mismatch fails preflight and opens or updates a configuration issue. The action
does not rewrite toolchain declarations.

Publication uses the built-in token and requires **Allow GitHub Actions to create
and approve pull requests** in **Settings → Actions → General → Workflow
permissions**. Default token permissions remain read-only. The workflow does
not approve or merge its own PRs.

The project currently has no npm dependencies. Its empty lockfile supports
repeatable validation and future dependency additions.
