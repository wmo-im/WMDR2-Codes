## Proposal: consolidate the three WMDR observing-method code lists

### Scope

The WMDR registry currently (19 August 2026) exposes three separate observing-method registers:

- `ObservingMethodAtmosphere`: **247** entries
- `ObservingMethodOcean`: **13** entries
- `ObservingMethodTerrestrial`: **54** entries

This gives **314 source rows** in the current registry export.

One domain-independent register is proposed to replace these domain-specific vocabularies:

`http://codes.wmo.int/wmdr2/ObservingMethod`

The three existing registers should remain resolvable for backwards compatibility, but should be frozen/deprecated once the unified register is available.

Source exports used for this draft:

- https://codes.wmo.int/wmdr/ObservingMethodAtmosphere?_format=csv&status=valid
- https://codes.wmo.int/wmdr/ObservingMethodOcean?_format=csv&status=valid
- https://codes.wmo.int/wmdr/ObservingMethodTerrestrial?_format=csv&status=valid

### Principle for the consolidated vocabulary

`ObservingMethod` should describe the **bio-physico-chemical principle or methodology by which an observed property is determined**.

It should not primarily encode:

- manufacturer, brand, model, or instrument family;
- sampling hardware or sample treatment;
- a recording mechanism;
- a software/model package;
- whether the observation was made by a human or automatically;
- `unknown` or `inapplicable`.

Those concepts belong elsewhere in WMDR/WMDR2, for example in instrument metadata, sampling/treatment metadata, procedure/protocol metadata, `SourceOfObservation`, a derivation/processing-method vocabulary, or a `nilReason`.

A useful test is:

> Would this observing method still be valid if the instrument were replaced by another instrument using the same scientific principle?

If the answer is no, the existing entry is probably describing the instrument rather than the method.

### Duplicate notations

Simply combining the three current tables causes numeric notation collisions:

| Notation | Atmosphere | Terrestrial/hydrological |
|---|---|---|
| `344` | GC-CVAFS | Laser ranging |
| `345` | GC-TCD | Echo sounding |
| `346` | Electrostatic collection + alpha counting | Frequency domain reflectometry |
| `347` | Tunable Infrared Laser Differential Absorption Spectroscopy (TILDAS) | Gamma-ray attenuation |
| `348` | Radionuclide decay counting [general] | Non-recording gauge |
| `349` | IR-GFC | Staff gauge |

[To be discussed] `unknown` and `inapplicable` also occur in both the atmosphere and terrestrial registers. Should these be carried into the new `ObservingMethod` list, or rather become nil reasons?

### Exact duplicate labels

The current exports contain the following exact duplicate labels:

| Label | Legacy entries |
|---|---|
| Denuder | Atmosphere 98, 99 |
| Microtops | Atmosphere 151, 152, 153 |
| Microwave radiometry | Atmosphere 155, 156, 336 |
| Radio acoustic sounding system (RASS) | Atmosphere 181, 182 |
| GC-CVAFS | Atmosphere 344, 417 |
| GC-TCD | Atmosphere 345, 418 |
| Frequency domain reflectometry | Terrestrial 346, 419 |
| Gamma-ray attenuation | Terrestrial 347, 420 |
| Non-recording gauge | Terrestrial 348, 421 |
| Staff gauge | Terrestrial 349, 422 |
| unknown | Atmosphere, Terrestrial |
| inapplicable | Atmosphere, Terrestrial |

There are **300 distinct labels among 314 source rows**, before considering semantic duplicates and near-duplicates.

### Semantic duplicates / consolidation candidates

Some particularly clear or useful cross-domain consolidations are:

| Legacy concepts | Proposed unified concept |
|---|---|
| Conductometry; Electric conductivity (EC meter); Ocean Electrical conductivity | `electricalConductivityMeasurement` |
| Mercury-in-glass thermometer; Mercury-based thermometry | `mercuryColumnThermometry` |
| Liquid-in-glass thermometer (mercury-free); Fluid-based thermometry | `liquidColumnThermometry` |
| Potentiometry (ion-selective electrode); Potentiometry (pH meter); Ocean Potentiometric method | `potentiometry` |
| Cup anemometer variants; cup-type current meter | `rotatingCupVelocityMeasurement` |
| Propeller anemometer; propeller-type current meter | `propellerVelocityMeasurement` |
| (Ultra)sonic anemometer; acoustic ultrasonic velocity meter | `acousticTransitTimeVelocimetry` |
| Acoustic Doppler current meter; acoustic Doppler velocity meter | `acousticDopplerVelocimetry` |
| Microwave profiler/radiometry duplicates | `microwaveRadiometry` |
| GC-CVAFS duplicates | `gasChromatographyColdVapourAtomicFluorescence` |
| GC-TCD duplicates | `gasChromatographyThermalConductivityDetection` |
| Volumetric measurement; terrestrial Volumetric method | `volumetricMeasurement` (discussion needed) |

The cup/propeller/acoustic examples illustrate why domain should not be part of the method identity: the underlying physical principle is independent of whether the moving fluid is air or water.

### Instrument-like entries

A substantial part of the current lists names instruments rather than methods. Examples include Brewer, Dobson, Microtops, ECC sonde, cup/propeller anemometers, MARGA, aethalometer/PSAP-like instruments, ceilometer, soil thermometer, staff gauge, acoustic Doppler current profiler, and radar water-level gauge.

Where the principle is sufficiently clear, the migration table maps these to method-oriented concepts. Examples:

| Legacy entry | Proposed method |
|---|---|
| Brewer / Dobson | `differentialUltravioletAbsorptionSpectrophotometry` |
| ECC sonde | `electrochemicalConcentrationCell` |
| Brewer-Mast / Brewer-GDR / Indian / carbon-iodine sondes | `potassiumIodideElectrochemicalOzoneMeasurement` |
| Ceilometer | `elasticBackscatterLidar` |
| Radar water-level gauge | `radarRanging` |
| Pressure-actuated recording gauge | `hydrostaticPressureWaterLevelMeasurement` |
| Wire-float recording gauge | `floatWaterLevelMeasurement` |
| Electromagnetic current meter | `electromagneticVelocimetry` |
| MARGA | `ionChromatography` plus separate sampling/treatment metadata |

These mappings are generally marked `discussion needed` because the legacy term may not contain enough information to guarantee a lossless conversion.

### Sampling/treatment and composite entries

Entries such as:

- Filter sampling + alpha counting
- Filter sampling + gamma spectrometry
- Filter + beta attenuation
- Filter + light attenuation
- Filter sampling + extraction + ion chromatography
- Adsorption tube + elution + gas chromatography
- Electrostatic collection + alpha counting

combine several conceptually different stages.

[To be discussed] The preferred representation should be:

`sampling strategy -> sample treatment -> observing/analytical method`

rather than minting a separate `ObservingMethod` for every combination.

The migration crosswalk therefore uses `split` for these cases.

### Software, procedures and source of observation

`FLEXPART`, `FLEXTRA`, `HYSPLIT`, `LAGRANTO` and generic transport modelling are derivation/modelling methods rather than direct observing methods.

`EUSAAR protocol` is a procedure/protocol.

[To be discussed] `Human observation` belongs under `SourceOfObservation`.

[To be discussed] `unknown` and `inapplicable` belong under `nilReason`.

### Proposed notation convention

The new notation should be readable and stable enough to serve as part of a semantic-web URI.

Proposed rules:

1. Prefer **spelled-out words** over acronyms.
2. Use lower camel case for ordinary words, for example:
   - `eddyCovariance`
   - `electricalConductivityMeasurement`
   - `gasChromatographyMassSpectrometry`
   - `acousticDopplerVelocimetry`
3. **Do not lower-camel-case acronyms that remain.** Retained acronyms stay in their established uppercase form, for example `TILDAS`, `colorimetryNEDA`, or `environmentalDNAAnalysis`.
4. Chemical formulae retain scientific case, for example `HgO`.
5. Lexicalized technical words such as *radar* and *lidar* may be treated as normal words.
6. In this draft, aim for **<= 48 characters**, with a practical hard limit of **50 characters**. If spelling out a well-established name would exceed that limit or substantially reduce readability, retain the established acronym in uppercase.

The current draft has no notation longer than 50 characters. `TILDAS` is deliberately retained as an uppercase acronym because the fully expanded notation would be longer than the proposed limit.

### Review status

The draft register and migration crosswalk contain a `reviewStatus` column with two values:

- `non-controversial` — exact duplicates, spelling/notation normalization, obvious acronym expansion, or a direct method-oriented reformulation with no material semantic change.
- `discussion needed` — semantic merges, cross-domain generalization, instrument-to-method inference, decomposition of composite entries, ambiguous legacy concepts, or newly proposed methods.

This is intended to let reviewers focus first on the cases where expert judgement is actually needed.

### Draft result

The first-pass curation of all **314 legacy rows** currently gives:

- **203** `rename`
- **59** `merge`
- **26** `move`
- **22** `split`
- **4** `nilReason`

The resulting draft unified register contains **230 legacy-derived method concepts**. A further **18 candidate missing methods** are included, giving **248 proposed concepts** in total.

Of the 314 legacy mappings, **174** are currently (19 August 2026) marked `non-controversial` and **140** `discussion needed`.

This is a curation draft, not a claim that all 248 concepts should be accepted unchanged.

The current source material is also uneven in its definitions: the atmosphere export contains many entries without a description. The proposed register therefore includes a `definitionStatus` field. In this draft, **130** definitions are drafted, **27** are inherited from the legacy registers, and **91** still need a definition.

### Candidate missing methods

The draft adds the following candidates for discussion:

- `platinumResistanceThermometry` — Platinum resistance thermometry
- `thermoelectricThermometry` — Thermoelectric thermometry
- `resonantPressureMeasurement` — Resonant pressure measurement
- `capacitivePressureMeasurement` — Capacitive pressure measurement
- `amperometry` — Amperometry
- `coulometry` — Coulometry
- `polarimetricRadar` — Polarimetric radar
- `satelliteNavigationAtmosphericDelay` — Satellite-navigation atmospheric-delay measurement
- `satelliteRadioOccultation` — Satellite radio occultation
- `satelliteNavigationReflectometry` — Satellite-navigation reflectometry
- `photogrammetry` — Photogrammetry
- `automatedImageClassification` — Automated image classification
- `microscopy` — Microscopy
- `flowCytometry` — Flow cytometry
- `bioacousticDetection` — Bioacoustic detection
- `molecularAssay` — Molecular assay
- `environmentalDNAAnalysis` — Environmental DNA analysis
- `laserInducedBreakdownSpectroscopy` — Laser-induced breakdown spectroscopy

These are deliberately marked `discussion needed`. In particular, the biological/image-processing candidates need agreement on how broad the scope of `ObservingMethod` should be, and some may ultimately belong in a separate processing/analysis vocabulary.

### Migration path

1. Create `http://codes.wmo.int/wmdr2/ObservingMethod` and make it the canonical vocabulary for new metadata.
2. Freeze/deprecate the three legacy registers, but keep all existing URIs permanently resolvable.
3. Publish a complete machine-readable crosswalk from every legacy URI to the new vocabulary or to the appropriate replacement construct.
4. Use explicit mapping relations:
   - exact mapping for true duplicates/renames;
   - close/broader/narrower mappings where semantics have changed;
   - split mappings where one legacy entry encoded several concepts.
5. Do not force a one-column substitution for instrument-like entries. Migration may need to populate both instrument metadata and a new method concept.
6. Convert legacy `unknown` / `inapplicable` to `nilReason`.
7. During a transition period, converters should accept all legacy URIs and normalize them to the new representation.
8. Validators can initially warn on legacy codes and later reject them for newly authored records while still accepting historical records.
9. Preserve provenance: the migration table should retain legacy register, notation, label and URI so that every conversion is auditable.

### Files in this proposal

- `ObservingMethod-proposed.csv` — one row per proposed unified method concept, including `reviewStatus`, definition status, provenance and legacy mappings.
- `ObservingMethod-migration.csv` — complete crosswalk for all 314 current source rows, including disposition, proposed replacement, mapping relation, target vocabulary and rationale.

The next useful review step is to work through the `discussion needed` subset, starting with the cross-domain semantic merges and the instrument-to-method mappings. Only after that should the new register be treated as stable.
