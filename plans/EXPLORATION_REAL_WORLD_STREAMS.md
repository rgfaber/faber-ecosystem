# EXPLORATION: Free public time-based data streams as neuroevolution environments

**Status:** exploration, pre-design. Not a charter, not an experiment.
**Date:** 2026-07-23
**Gate intended:** DESIGN (adversary attacks the *logic*, no runner exists)

---

## 0. The framing problem, stated before the catalogue

Neuroevolution does not consume data. It consumes an **environment**: something that
turns an action into a consequence and eventually into a scalar. A time series is not
an environment. It is a tape.

Everything below therefore gets sorted by *how* a tape becomes an environment, because
that choice, not the richness of the tape, decides whether the result says anything
about neuroevolution at all.

### Three couplings, only one of which is native to this engine

**Class B, replay as open loop.** Stream to observation, net emits a number, error to
fitness. The net's output never touches the next observation. This is supervised
learning wearing a costume. Gradient descent on an LSTM or a boosted tree will normally
beat an evolved net at it, and will do so with 1000x less compute. Any Class B result
that omits a gradient-trained arm is not a claim about neuroevolution, it is a claim
about faber-tweann's convenience. Class B earns its place only when the loss is
genuinely hostile to gradients: discrete, asymmetric, thresholded, or defined over a
whole trajectory (for example "number of hours the battery was empty when the price
spiked", which has zero gradient almost everywhere).

**Class A, replay plus a consequence model.** The stream supplies *exogenous* drivers
that no agent of ours could influence (price, irradiance, demand, arrivals). A small
explicit model supplies the *endogenous* state that the action does move (battery
state of charge, van position, inventory, queue). The world does not react, but the
agent's own state does, so the problem is genuinely sequential: delayed reward, partial
observability, non-differentiable constraints, path dependence. This is neuroevolution's
home turf and it is the only class where a positive result would be interesting rather
than embarrassing.

**Class C, stream as opponent.** The stream is produced by many adapting agents (an
order book, a fee market, a road network). Tempting to call it coevolution. It is not.
Insight 057 already settled the relevant point in a controlled setting: evolving against
a *frozen* opponent, however diverse, is a curriculum, not coupling. A replayed market
cannot react to us, so Class C on historical data is Class A with a fancier exogenous
driver. Calling it an arms race would repeat exactly the overclaim that two CLAIM gates
deflated in 057.

**Consequence for the whole exploration:** the interesting question is not "which stream
is richest" but "which stream comes with a *credible, cheap, honest consequence model*".
Most do not. The shortlist in section 4 is ordered by that, not by data volume.

> **Adversary correction (DESIGN gate, 2026-07-23).** The three-class split is oversold.
> On replay, C reduces to A by this document's own argument, and B is defined as
> degenerate. The operative taxonomy is therefore **one bit**: is there a consequence
> model that the agent's own state feeds, yes or no. C is a null category that exists
> only for live interaction the programme is not proposing. Keeping the three labels is
> fine as vocabulary; treating them as structure is not.

---

## 1. Scorecard: ten axes, each tied to something the corpus already knows

| # | Axis | Why it decides value | Corpus link |
|---|------|---------------------|-------------|
| 1 | **Consequence** | Without it, Class B, and gradients win | 024 (optimizer wall), whole P2 arc |
| 2 | **Partial observability** | If a Markov feature set trivialises it, memory cannot pay and the task cannot separate substrates | 016, 019, 023 |
| 3 | **Non-stationarity with a marker** | Regime shifts are what plasticity is *for*; needs to be locatable in time, not just believed in | 031-046, esp. 046 (post-deployment adaptation) |
| 4 | **Knowable predictability ceiling** | Without an upper bound, a mediocre score is uninterpretable | Spartan: kernel-memory falsified against a 61.6% Bayes ceiling |
| 5 | **External graded benchmark** | Many operators publish *their own forecast*. Free, strong, aligned, graded reference | 054 (a fixed benchmark must be graded, a weak one saturates) |
| 6 | **Replay reproducibility** | Is history immutable and re-fetchable, or silently revised? | 047+ provenance standard |
| 7 | **Event time vs observation time** | The single most common leak in real streams | new, no corpus precedent |
| 8 | **Episode supply** | n>=10 *independent* runs needs independent data windows, not overlapping slices | standing rule |
| 9 | **Shardable by geography** | Natural island model, one region per beam node | P5 charter |
| 10 | **Licence and sovereignty** | Commons-compatible, EU-hosted, no Big Tech in the data path | strategic anchor |

Axis 4 and axis 5 deserve emphasis because they are where this exploration most differs
from the usual "cool free APIs" list. **A stream that publishes its own operator forecast
is worth more than a stream with ten times the resolution**, because it hands over a
graded benchmark for free. If the evolved net cannot beat the operator's published
day-ahead forecast, the claim is dead on arrival, and we learn that in a day rather than
a month.

> **Adversary correction: ten axes is padding, five are load-bearing.**
> Axis 5 (operator forecast) is an *instance* of axis 4 (ceiling), since an operator
> forecast is an estimate of the ceiling. Scoring both double-counts, and it
> double-counts precisely on Elia and Open-Meteo, the two sources this author had
> already chosen. That is a prior preference laundered into two scores, and it is
> exactly the failure mode question 5 of section 7 was fishing for.
> Axis 7 (event vs observation time) is a subset of axis 6 (honest replay).
> Axis 9 (shardable by geography) is not a property of environment quality at all, it
> is fit-to-an-open-charter; axis 8 already covers the statistical need.
> Axis 10 (licence) is a publication constraint, real but not scientific value.
>
> **The five independent axes: consequence, partial observability,
> non-stationarity-with-an-externally-marked regime, ceiling-or-graded-benchmark,
> honest-replay-with-episode-supply.** The other five are subsidiary and are retained
> in the table only as operational checklist items.
>
> **The axis nobody wrote down:** the scorecard grades the *stream* and never grades
> the *engine* against the task. Faber's evolved substrates in P3 and P7 are on the
> order of [2,6,4]. Nothing here establishes that a net of that scale can represent a
> policy for bike-share rebalancing, battery frequency containment, or execution. A
> bridge that fails for want of capacity will be misread as a statement about real
> data. Capacity has to be argued per task before the task is adopted.

---

## 2. Catalogue

Cadence is publication cadence. "Key" means an API key or token is needed. "Own
forecast" means the source also publishes a forecast of the same quantity, usable as
the graded benchmark of axis 5.

### 2.1 Energy and grid

| Source | Cadence | History | Key | Own forecast | Class | Notes |
|---|---|---|---|---|---|---|
| **Elia Open Data** (BE) | 1 min (imbalance price), 15 min | years | no | **yes** (load, solar, wind, imbalance price forecast) | A | Opendatasoft API, CSV/JSON/Parquet export. Belgian, sovereign, directly on the Track A axis. |
| **ENTSO-E Transparency** (EU-wide) | 15/60 min | 2015+ | token, free by email | yes (day-ahead load, wind, solar) | A | Migrating to "TP R3": API stability is a live risk. Values **are revised** retroactively. |
| **Elexon Insights / BMRS** (GB) | 5 min to 30 min | years | **no key** | yes | A | Richest free balancing-market data anywhere. IRIS push service is free near-real-time. Bid/offer stacks expose actual agent behaviour. |
| **Fingrid Open Data** (FI) | 3 min (frequency), 1 min some | years | key, free | yes | A | Cleanest API of the TSOs. |
| **mainsfrequency.com / netzfrequenzmessung.de** | 10 s | rolling | no | no | A/B | Continental synchronous area frequency. Real physical dynamics. |
| **power-grid-frequency.org database** | 1 s (archived) | multi-year | no | no | A/B | Research-ready cleaned archives. The right source for 1 s frequency, not the live scrapes. |
| **Electricity Maps** | hourly | limited free | key | yes | B | Free tier is thin; carbon intensity only. |

Grid frequency deserves a specific note. It is the closest thing in the free world to a
**continuous control signal from a real physical plant**: second-scale, driven by a
genuine imbalance between generation and load, with visible deterministic structure
(the notorious deviation at each hour boundary as schedules step) plus stochastic
noise. A Class A coupling exists and is cheap: a synthetic battery doing frequency
containment, rewarded for keeping frequency in band at minimum throughput. That is a
real control task with a real signal and a non-differentiable objective (deadbands,
droop, energy budget).

### 2.2 Weather and atmosphere

| Source | Cadence | History | Key | Own forecast | Class | Notes |
|---|---|---|---|---|---|---|
| **Open-Meteo** | hourly, 15 min for some | **1940+** (ERA5) | no | **yes, and past model runs** | A/B | CC-BY. 10k calls/day free, non-commercial. The "previous runs" endpoint gives the forecast *as it was issued*, which is the leak-free way to build a benchmark. |
| **DWD Open Data** | 5 min (radar), hourly (MOSMIX) | years | no | yes | A/B | Raw ICON output and station forecasts. Bulk over plain HTTP. |
| **KNMI Data Platform** | 10 min | years | key, free | yes | B | Dutch stations, radar. |
| **NOAA/NWS API + MRMS** | 2-5 min radar | years | no | yes | B | US. Huge. |
| **NOAA SWPC** (space weather) | 1 min | 7 days live, archives | no | partial (Kp forecast) | B | Solar wind plasma and IMF. Instrument changed recently (SOLAR-1 now operational alongside/replacing DSCOVR), which is itself an instrumentation non-stationarity trap. |

Weather is the best *exogenous driver* in the catalogue and a poor *primary target*.
Beating the national met service at its own game with an evolved net is not a plausible
result; using its forecast as a covariate and its error as the uncertainty an agent must
act under is exactly right.

### 2.3 Transport, mobility, logistics

| Source | Cadence | History | Key | Own forecast | Class | Notes |
|---|---|---|---|---|---|---|
| **GBFS bike-share** (1000+ systems) | 10-60 s | none published, must self-capture | mostly no | no | **A** | `station_status` gives bikes and docks per station. Rebalancing is a genuine vehicle-routing control problem with a credible simulator. Strong daily/weekly cycle, strong weather coupling. |
| **GTFS-Realtime** (6000+ feeds via Mobility Database) | 10-30 s | none, self-capture | mostly no | scheduled time = benchmark | A | Delay propagation through a network. The *schedule* is a free graded benchmark: predicting "on time" is the persistence baseline. |
| **NDW** (NL) | 1 min | yes, open archive | no | no | A | 24k+ loop locations, flows and speeds, DATEX II. Ramp metering and speed harmonisation are real action models. |
| **AISstream.io** | seconds | none, self-capture | free key | no | A/C | Global AIS over websocket, free. Port congestion, ETA, anchorage queueing. Genuinely logistics. |
| **OpenSky Network** | 5-10 s | yes (academic) | **OAuth2 since 2026-03-18**, credit-limited (~4000/day, ~8000 for feeders) | no | A/C | Credit system makes dense polling infeasible without a receiver. |

Bike-share is quietly the strongest Class A candidate in the entire catalogue, and it
is the one nobody puts on these lists. Reasons: the state is small and fully
enumerable (N stations x bikes), the exogenous driver (demand) is real and
weather-coupled, the action (move k bikes from station i to j with a van, subject to
capacity and time) is discrete and non-differentiable, the reward (unmet demand) is
delayed and sparse, and a faithful simulator is a hundred lines. Every property on the
scorecard is satisfied without pretending.

### 2.4 Finance and markets

| Source | Cadence | History | Key | Own forecast | Class | Notes |
|---|---|---|---|---|---|---|
| **Crypto exchange public websockets** (Binance, Kraken, Coinbase, Bitstamp) | ms | none free, self-capture | **no** | no | C then A | Full depth and trades, no auth. Highest volume, most adversarial, lowest direction-predictability. |
| **mempool.space websocket** | per block and per mempool update | rolling | no | **yes** (its own fee recommendations) | **A** | Bitcoin fee market: a congestion game with an explicit action (fee bid) and explicit consequence (confirmation delay). Small state, honest simulator (block template selection is deterministic given the mempool). |
| **FRED / ALFRED** | monthly to daily | decades | key, free | consensus series exist | B | **ALFRED serves vintages**, i.e. the data as it was known at each past date. This is the correct antidote to revision leakage and almost nobody uses it. |
| **ECB Data Portal, frankfurter.dev** | daily | decades | no | no | B | Reference FX and euro-area macro. Too slow for episode supply. |

The direction-prediction trap must be stated plainly, because it is where this kind of
exploration usually dies. On liquid markets at short horizons the achievable edge over a
random walk is near zero, and a neuroevolution run that reports 52% directional accuracy
on one split is reporting noise. The Spartan programme already ate a version of this
lesson: an intervention was falsified against a 61.6% Bayes ceiling that had to be
computed before the claim meant anything. Markets have *no* known ceiling, which makes
them the worst possible place to look for one.

There are two honest market targets: (a) quantities that are demonstrably predictable
(realised volatility, spread, queue depth, order arrival intensity), where a ceiling can
be estimated from the data's own autocorrelation structure, and (b) execution as
control: given an exogenous replayed book, minimise the cost of buying X units in T
minutes. (b) is Class A, has a credible simulator (subject to a stated market-impact
assumption), a non-differentiable objective, and a strong known benchmark (TWAP/VWAP).
That is a real problem. "Predict the price" is not.

### 2.5 Internet, infrastructure, civic

| Source | Cadence | History | Key | Own forecast | Class | Notes |
|---|---|---|---|---|---|---|
| **RIPE RIS Live** | sub-second | yes (RIS dumps, 5 min MRT) | no | no | B/C | Full BGP update firehose over websocket, filterable. Highly non-stationary, event-driven, heavy-tailed. |
| **Wikimedia EventStreams** | continuous SSE | yes | no | no | B | **Carries delayed ground truth**: an edit that is reverted within N minutes is a labelled bad edit. Free labels are rare. |
| **GH Archive** | hourly batch | 2011+ | no | no | B | Complete, immutable, replayable. Excellent for offline method work. |
| **Cloudflare Radar** | minutes | yes | key, free | no | B | Aggregate traffic, outages, protocol mix. |

### 2.6 Environment, earth, water

| Source | Cadence | History | Key | Own forecast | Class | Notes |
|---|---|---|---|---|---|---|
| **Sensor.Community** | ~2.5 min per sensor, 5 min archives | daily CSV archives, years | no | no | A/B | Thousands of DIY sensors. **Drift, dropout, miscalibration and correlated failure are features, not bugs**, for a robustness rung. |
| **OpenAQ v3** | hourly | years | key, free | no | B | Reference-grade, harmonised. |
| **USGS earthquakes GeoJSON** | ~1 min | decades | no | no | B | Point process. Near-unpredictable by construction. |
| **USGS Water Services / Rijkswaterstaat waterinfo** | 5-15 min | decades | no | **yes** (astronomical tide) | A/B | Water level decomposes into a *deterministic* astronomical tide plus a stochastic surge. The analytic ceiling is known. |
| **Copernicus CDS / CAMS** | hourly, batch | decades | key, free | yes | B | Reanalysis and atmospheric composition. Slow retrieval. |

Two of these are proposed below as **calibration controls** rather than as targets.

---

## 3. Traps, ranked by how likely they are to invalidate a result

1. **Revision leakage.** ENTSO-E, most TSOs, and many traffic portals *rewrite history*.
   A model trained and evaluated on revised values has seen information that did not
   exist at decision time. Mitigation: use vintage-aware sources (ALFRED) where they
   exist, and otherwise **start our own append-only capture now**, stamping every record
   with both event time and observation time. This is cheap and local, so it costs
   nothing, but it cannot be done retroactively. It is the one action that has an
   opportunity cost for waiting.
2. **Publication lag mistaken for availability.** A value stamped 12:00 may be published
   at 12:45. Any feature vector built from the archive is, by default, a time machine.
   Every record needs two timestamps; the runner must filter on the observation one.
3. **The missing gradient baseline.** For any Class B task, an evolved net with no
   gradient-trained comparator is uninterpretable. The corpus standard already demands a
   null arm; on real data the honest null is *not* random, it is persistence *and* a
   gradient model *and* the operator's own published forecast.
   **Adversary correction:** as originally written this demand was an evasion. It was
   placed only on Class B, the class this document argues should not be run, and was
   absent from Class A, the recommended class. The load-bearing comparator for Class A
   is **gradient-based RL (PPO and kin) on the same consequence model under shared
   seeds**. That is the comparison that could show evolution is not worth the compute
   here, which is why it is the one that must be named rather than smuggled. It is a
   genuine scope change for the programme (from "which evolved configuration" to "is
   evolution the right tool"), and adopting a Class A task means accepting it.
4. **Correlated missingness.** Sensors fail *because* of the event (storms kill power,
   floods kill gauges, congestion kills feeds). Forward-filling encodes "nothing
   happened" precisely when everything happened. Missingness must be an explicit input
   channel, not a silent repair.
5. **n inflation by overlapping windows.** Ten runs on ten overlapping slices of one
   year is n=1 dressed as n=10. Independent data blocks with purge gaps, and the arms
   must share the *same* blocks. This is the data analogue of the common-random-number
   rule the corpus already enforces across arms, and it is stricter than the
   simulator case, because two different calendar periods are two different worlds.
6. **Non-stationarity as an excuse.** A drop in performance after a regime change is
   evidence for the plasticity story *only* if the regime change was located
   independently of the performance drop. Otherwise the marker is fitted to the result.
   Pre-register the regime marker (holiday calendar, storm warning, outage notice, halt)
   from an external source before looking at scores.
7. **Timezone and settlement arithmetic.** 15-minute MTUs, DST double hours, UTC vs
   local vs market time. Energy data is notorious. Silent one-hour misalignment produces
   a plausible, entirely wrong result.
8. **Rate limits and courtesy.** Poll once, cache forever, replay locally. Local compute
   is free; getting banned from Elia is not recoverable by spending money.
9. **Licence.** Open-Meteo's free tier is non-commercial with CC-BY attribution.
   ENTSO-E requires attribution and forbids some redistribution. This matters at
   publication time, not at experiment time, but it matters.
10. **TP R3 and API churn.** ENTSO-E is mid-migration; OpenSky changed auth in March 2026
    and killed basic auth. Pinning a stream is like pinning an engine commit: the
    capture, not the endpoint, is the record.

---

## 4. Shortlist

**Tier 1, worth capturing now.**

1. **Elia Open Data (BE).** No key, one-minute imbalance price and system imbalance,
   quarter-hourly solar and wind measured *and* forecast, load and load forecast.
   Satisfies axes 1 (via a battery or BRP action model), 3 (imbalance regimes are real
   and externally markable from outage notices), 5 (Elia publishes its own forecasts),
   9 (per-zone), 10 (Belgian, sovereign, on the Track A axis). Fat-tailed target, which
   makes the asymmetric-cost structure of insight 055 directly relevant: the cost of
   being short at a EUR 2000/MWh imbalance price is not the cost of being long at
   EUR 40.
2. **Open-Meteo.** No key, history to 1940, ensembles, and past model runs. The
   exogenous driver for nearly every Class A coupling above, and the source of an
   honest, leak-free benchmark.
3. **A GBFS system plus weather.** The cleanest genuine control problem in the free
   world. Small state, discrete actions, delayed sparse reward, credible simulator,
   real demand.

**Tier 2, strong but heavier.** Elexon Insights and IRIS (richer than Elia, GB rather
than BE); NDW traffic; mempool.space fee market (elegant congestion game, tiny state);
Sensor.Community (the robustness rung, see below); GTFS-RT delay propagation.

**Tier 3, interesting and trap-dense.** Crypto order books (only for volatility or
execution, never direction); RIS Live; AIS; OpenSky.

**Calibration controls, and this is the methodological point.** Every claim about a real
stream should be bracketed between two streams whose ceilings are known:

- **Near-unpredictable floor:** USGS earthquake occurrence. Any method that reports
  skill here is reporting a bug.
- **Near-deterministic roof:** the astronomical tide component of water level. Any
  method that fails to reach near-ceiling here cannot be trusted on anything harder.

Running both alongside a real target costs almost nothing and converts an
uninterpretable number into a position on a scale. This is the same instinct as insight
054 (a benchmark must be graded), applied to the data rather than to the opponent.

---

## 5. Proposed first rung, deliberately small

The tempting first experiment is "evolve a controller that beats the operator's forecast
on Belgian imbalance". That is three rungs too high, and P7 already demonstrated what
happens when the ladder skips steps: the claim gets deflated twice before a control
settles it.

**The honest first question is narrower: does real-world exogenous structure change a
conclusion the corpus has already settled under synthetic conditions?**

Concrete candidate, sized for one experiment: insights 034, 036 and 037 established that
sensor noise breaks CfC while evolved plasticity stays robust, under *Gaussian* noise
injected by the scape. Real sensor noise is not Gaussian. It is heavy-tailed,
autocorrelated, drifting, and it drops out in bursts that correlate with the event of
interest. So:

**The first draft of this rung did not survive the DESIGN gate.** It is preserved here
because the corpus does not hide failed designs, and the failure is instructive.

Draft as submitted: identical task and budget at the existing 034/037 operating point
(delay-8, sigma matched at 0.5), three noise sources, Gaussian versus resampled real
versus real-with-dropouts, decision rule "to be stated before running".

Why it fails, in the adversary's words and by its evidence:

- **Saturated operating point.** At delay-8, sigma 0.5, CfC is already floored (0/8)
  and plasticity already ceilinged (8/8 across the whole 037 grid). A margin pinned
  between floor and ceiling cannot move. The likely outcome is not a false positive, it
  is **no resolution**, which section 5 pre-framed as the positive reading ("survives
  realistic noise"). This is insight 054's saturation lesson, cited approvingly nine
  paragraphs earlier and then violated in the headline design.
- **Deferred decision rule plus both-outcomes-publishable** means no reachable result
  can embarrass the thesis.
- **Variance-matching is the wrong control.** A heavy-tailed source at matched variance
  is *smaller* than Gaussian most of the time with rare spikes, so it can be easier, not
  harder. A null would be uninterpretable between "shape does not matter" and "matched
  variance made it easier".
- **The dropout arm is a 038-class confound.** Dropouts that correlate with the event
  are an information channel; a solver exploiting them looks like robustness and is
  leakage.
- **Pseudoreplication in noise-world.** Noise drawn from one sensor trace is n=1 in the
  only dimension the claim is about, however many evolutionary runs sit on top.
- **No fitted-generative arm**, which is the same structural gap 057 had before its
  decoupled control existed: an effect would be attributed to "realism" when a
  generative arm would have localised it to a single statistic, or to nothing.

**Rebuilt pre-registration (adversary's version, adopted).**

- Task: delay-8 T-maze, ABCD plasticity versus CfC, sep-CMA-ES, budget and seeds shared
  **across** noise sources, not merely within.
- Noise sources, each swept in intensity: (a) Gaussian; (b) **fitted generative model**
  of real sensor noise, matched on marginal tail index and autocorrelation time;
  (c) raw resampled real trace, variance-matched to (a); (d) raw real trace with
  dropouts preserved; (e) **dropout-shuffled control**, same dropout rate, event
  correlation destroyed.
- Noise drawn from **>=10 independent sensors**; sensor identity is the independent unit
  for the noise claim.
- Measure **sigma_50**, the intensity at which plasticity solves 5/8, per source. Report
  the pair (plastic, CfC) at each intensity, never the difference alone.
- Decision rule, fixed now: "realism collapses the margin" if sigma_50 for (c) is >=30%
  below (a) **and** (b) reproduces the shift, which localises the cause to a modellable
  statistic. "Realism matters via un-modelled structure" if (c) shifts and (b) does not.
  Null, "realism is irrelevant at matched variance", if (a), (b), (c) agree within 15%.
  Dropout result is rejected as leakage if (d) beats (e), accepted as mechanism only if
  (d) and (e) agree.
- Committed fallback: the null is a bounded publishable finding that the P3 robustness
  margin is a property of noise *variance*, not noise *shape*, and licenses **no**
  "real data was needed" claim.
- The Gaussian arm is re-estimated, not assumed. 034/036/037 are n=8, one task, one
  delay, stochastic single-episode fitness. If the rung disagrees with them, the null
  hypothesis is that the synthetic anchor was n=8 noise, not that the real data is
  special.

Only after a rung like that should Class A energy or bike-share follow, since those need
a consequence model, and a consequence model is a second thing that can be wrong.

---

## 6. Enabling step, independent of any experiment

Whatever gets run, **the capture should start now**, because history cannot be captured
retroactively and revision destroys it. Requirements: append-only, event time plus
observation time on every record, raw payload preserved verbatim, one region per node,
no interpretation at ingest. Cost is a few MB per day per stream and zero LLM tokens.
This is deliberately not a proposal to build anything clever; it is a proposal to not
lose the only thing that cannot be re-obtained.

**Built, 2026-07-24.** The capture is no longer a proposal:

- `hecate-services/hecate-archive` — the collector. Many sensors, one archive, the same
  cardinality as warden to sentinel. Verbatim bytes, length-prefixed CBOR, hourly
  segments sealed with a SHA-256 over the uncompressed stream, and a gap ledger that
  publishes its own holes. Contract in `DESIGN_ARCHIVE_CONTRACT.md`.
- `hecate-services/hecate-grid` — the first sensor. Seven Elia near-real-time datasets,
  chosen so that measurement and Elia's own published forecast of the same quantity are
  both captured, which is axis 5 satisfied for free.

Measured rather than assumed: ~324 bytes per Elia row, ~340 ms round trip, ~13 MB/day
raw for the whole default set, under 2 MB/day sealed. The first sizing was a hundredfold
over-sampled and would have been ~320 MB/day; it was fixed by measuring, not by
planning.

Two adversary corrections to that paragraph, both sharp:

1. **The capture harness is a runner.** Saying "the capture is the record" inverts the
   047 standard. An ingest that stamps timestamps and parses payloads can be wrong and
   internally consistent, which is the 038/040 failure mode exactly. The harness is
   versioned, pinned and archived under the same rule as an experiment runner; only the
   verbatim payload is the raw feed.
2. **Capture without a pre-committed split is a selection effect in slow motion.**
   Append-only protects against revision, not against choosing windows, sensors and
   zones after seeing the data. The independent-blocks-with-purge-gaps rule of trap 5
   must be committed **at capture time**, not at analysis time.

---

## 7. Questions for the adversary

1. Is the Class A/B/C split load-bearing, or is it a distinction that collapses once a
   consequence model is admitted to be a simulator like any other scape? If every Class
   A coupling is just "a scape with a recorded driver", what does the real driver buy
   that a fitted generative model of the driver would not?
2. The gradient-baseline demand in trap 3: is it actually necessary, or does it smuggle
   in a comparison the programme never signed up for? The corpus compares *evolutionary*
   configurations against each other. Does inviting SGD in change what the programme is?
3. Are the two calibration controls (earthquakes as floor, tide as roof) genuinely
   informative, or do they merely bracket a number without constraining it?
4. Is the proposed first rung a real test, or is it a guaranteed positive? Heavy-tailed
   noise with dropouts breaking a leaky integrator seems close to a foregone conclusion,
   and a foregone conclusion is not an experiment.
5. Which of the ten scorecard axes are actually redundant with each other, and which
   have I invented to justify a source I already liked?

---

## 8. Adversary verdict (DESIGN gate, faber-adversary, 2026-07-23)

Ruling: *"Fix the operating point and add a fitted-noise control and the first rung
becomes a real test; leave them and it is a confirmation exercise dressed as a bridge."*

Objections, ranked as the adversary ranked them, with disposition:

| # | Objection | Disposition |
|---|---|---|
| 1 | First rung runs where the metric is saturated (034/037 floor and ceiling); violates 054 | **Accepted.** Section 5 rebuilt around sigma_50 sweep. |
| 2 | Decision rule deferred, both outcomes pre-declared publishable | **Accepted.** Rule and fallback fixed in section 5. |
| 3 | Variance-matching is the wrong control; dropout arm is a 038-class confound | **Accepted.** Match on the claimed statistic; dropout-shuffled control added. |
| 4 | A fitted generative model of the driver dominates raw replay; the premise is untested by its own rung | **Accepted.** Fitted-noise arm added. This is the strongest objection: real data buys exactly one thing, the un-modelled structure, which is by construction the part that cannot be attributed. |
| 5 | Gradient baseline demanded only in Class B, where it is safe, absent in Class A, where it threatens | **Accepted.** Trap 3 amended; the scope change is named. |
| 6 | A/B/C taxonomy collapses to one bit on replay | **Accepted.** Noted in section 0. |
| 7 | Noise pseudoreplication if drawn from one sensor | **Accepted.** >=10 sensors, sensor as independent unit. |
| 8 | "The capture is the record" contradicts 047's runner-is-the-record | **Accepted.** Section 6 amended. |

Of the author's five doubts in section 7, the adversary rated 1, 4 and 5 correct and
sharpened them, and rated 3 (calibration controls) an over-worry: cheap, fine, and
limited only in the way already stated. What the author **failed** to doubt: the
saturated operating point, the ambiguity of a variance-matched null, that Class A's
exogenous-only design is precisely the regime a fitted model wins, that the gradient
baseline was placed where it could not hurt, and that the synthetic anchor it proposed
to reconcile against is itself n=8.

Standing consequence for the catalogue: **no source in section 2 has yet earned an
experiment.** The catalogue is a map of what could be captured, and section 6 is the
only action it presently justifies.
