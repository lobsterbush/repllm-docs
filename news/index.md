# Changelog

## repllm 0.4.0

### Submission audit

- Prevent conversation history from leaking into generation or synthetic
  rating.
- Replace condition-revealing material IDs with random codes in human
  sheets; restore original IDs on import and retain support for earlier
  sheet formats.
- Refuse to overwrite existing rating sheets or keys. Preserve leading
  zeroes in imported IDs and reject duplicate keys, duplicate ratings,
  and missing dimensions.
- Validate scalar counts, dimension descriptions, scale endpoints,
  material IDs, and confidence levels. Count empty generation responses
  as failures.
- Require `estimatr` for the advertised robust inference default.
- Correct the sensitivity-result print method and condition-column
  examples.
- Declare AI – Human (editor) authorship provenance and redesign the
  documentation to match Charles Crabtree’s professional website.

### Scope change

The package is now about generating experimental treatments with a model
and validating them before you field them. I’ve taken out the
text-annotation workflow: `llm_run()`, `run_coding()`, reliability
against a gold standard, prompt and model sensitivity, LaTeX reporting,
and audit logging. If you want to annotate text, use `ellmer` directly.
It covers structured output, batching, retries, and cost accounting on
its own now, and there was no good reason for me to sit on top of it.

### Validation in three tiers

- Automatic
  ([`validate_auto()`](https://lobsterbush.github.io/repllm-docs/reference/validate_auto.md)):
  [`check_length_balance()`](https://lobsterbush.github.io/repllm-docs/reference/check_length_balance.md),
  [`check_readability()`](https://lobsterbush.github.io/repllm-docs/reference/check_readability.md),
  [`check_manipulation_leakage()`](https://lobsterbush.github.io/repllm-docs/reference/check_manipulation_leakage.md),
  and
  [`check_lexical_overlap()`](https://lobsterbush.github.io/repllm-docs/reference/check_lexical_overlap.md).
  Runs locally with no API key.
  [`check_readability()`](https://lobsterbush.github.io/repllm-docs/reference/check_readability.md)
  delegates to `quanteda.textstats` when available and records which
  method produced the score.
- Synthetic
  ([`synthetic_ratings()`](https://lobsterbush.github.io/repllm-docs/reference/synthetic_ratings.md),
  [`synthetic_check()`](https://lobsterbush.github.io/repllm-docs/reference/synthetic_check.md)):
  a model rates every material on the target construct, blind to
  condition and in shuffled order. Ratings come back through
  [`ellmer::parallel_chat_structured()`](https://ellmer.tidyverse.org/reference/parallel_chat.html)
  with a `type_object()` schema, so the model can’t hand you an
  off-scale value and there’s nothing to parse.
- Human
  ([`sample_for_human_validation()`](https://lobsterbush.github.io/repllm-docs/reference/sample_for_human_validation.md),
  [`export_rating_task()`](https://lobsterbush.github.io/repllm-docs/reference/export_rating_task.md),
  [`import_human_ratings()`](https://lobsterbush.github.io/repllm-docs/reference/import_human_ratings.md),
  [`human_check()`](https://lobsterbush.github.io/repllm-docs/reference/human_check.md)):
  blinded rating sheets with a separate key, read back into the same
  long format the synthetic tier produces.

### Generation

- [`generate_materials()`](https://lobsterbush.github.io/repllm-docs/reference/generate_materials.md)
  now takes a single shared `chat`. It used to build one Chat per prompt
  and pass the first to `parallel_chat()`, which made the first
  condition’s instruction the system prompt for every generation and
  confounded the whole design. That was a bad bug and I’m glad it’s
  gone.
- [`generate_materials()`](https://lobsterbush.github.io/repllm-docs/reference/generate_materials.md)
  warns when the shared system prompt names one of your factor levels.
- Generation provenance (model, params, template hash, seed, failure
  count) is stored on the returned object.
- [`generation_sensitivity()`](https://lobsterbush.github.io/repllm-docs/reference/generation_sensitivity.md)
  runs the same design under several generators.
- [`design_conditions()`](https://lobsterbush.github.io/repllm-docs/reference/design_conditions.md)
  replaces `cross_design()` and crosses experimental factors rather than
  chats and temperatures.

### Correctness

- Stratified sampling no longer hits R’s
  [`sample()`](https://rdrr.io/r/base/sample.html) length-one trap,
  which could return a wrong index and duplicate rows; `min_per_stratum`
  guarantees every condition is represented; allocation now returns
  exactly `n`, and no longer errors when there are more strata than
  requested draws.
- Missing texts, failed API calls, and blank ratings are counted and
  reported everywhere rather than being dropped from denominators.
- Standard errors are clustered by material when several raters rate the
  same material.
- Term matching escapes regex metacharacters and anchors word boundaries
  only next to word characters, so codebook labels such as
  `strongly agree (2)` match correctly.

### Example data

The bundled datasets are replaced by one complete simulated validation
study, so the whole pipeline runs without an API key.

- `repllm_materials`: 24 generated carbon-tax vignettes, three frames
  crossed with two speaker types, four realisations per cell.
- `repllm_synthetic`: 216 ratings, three LLM raters x 24 materials x
  three dimensions.
- `repllm_human`: 144 ratings, two human coders over the same materials
  and dimensions.

I built them to be instructive rather than flattering. The materials are
balanced on length and reading level, but two of them name their own
condition, so
[`validate_auto()`](https://lobsterbush.github.io/repllm-docs/reference/validate_auto.md)
comes back needing attention. Both rating tiers recover all three
intended manipulations, and the two tiers correlate at about 0.9 on
every dimension with essentially no mean difference. The model tier
still reads the economic and moral gaps as wider than the coders do, and
the scientific gap about the same. That split is worth looking at by
eye.

The annotation-era datasets `repllm_example`, `repllm_example_run`, and
`repllm_vignettes` are removed.

### Removed

- `compare_validation()` is gone, along with the `validation_comparison`
  class. It reduced the two rating tiers to one pass-or-fail verdict
  built on `gap_inflation`, the difference between each tier’s
  standardised effect. Each tier was divided by its own pooled SD, and
  that SD carries the rater’s noise, so a quiet model rater produced a
  large apparent effect and a noisy human coder a small one. On
  simulated data where both tiers agreed exactly about every material,
  the metric read +0.55, past the default threshold that fails a study.
  The quantity was measuring rater consistency as much as rater
  disagreement, so it should not have been a gate.

  Run
  [`synthetic_check()`](https://lobsterbush.github.io/repllm-docs/reference/synthetic_check.md)
  and
  [`human_check()`](https://lobsterbush.github.io/repllm-docs/reference/human_check.md)
  on the same materials and read the two recovery tables side by side.
  The correlation and mean difference the old function reported were
  sound and are a couple of lines of
  [`aggregate()`](https://rdrr.io/r/stats/aggregate.html) and
  [`cor()`](https://rdrr.io/r/stats/cor.html); the vignette shows them.

### Inference

- `.rating_effects()` now refuses to report a standard error, interval,
  or p-value when any condition rests on a single material. The
  condition effect is perfectly confounded with that material, and the
  cluster-robust variance collapses to about 1e-15 rather than to `NA`,
  so this was previously reported as `p < 1e-15` for an effect that is
  not identified at all.
- Contrasts whose standard error sits at the scale of rounding error now
  have their interval and p-value withheld with a warning, rather than
  printing something like `p = 2e-31`. Point estimates are still shown.
- Simulation puts interval coverage at nominal from roughly 20 materials
  upward, and below nominal beneath that. The existing warning fires
  under 20 clusters. Coverage also degrades when raters differ in how
  strongly they react to the manipulation, because standard errors
  cluster on material while raters are crossed with materials; that is a
  known limitation, not fixed.

### Reporting

- The quantity printed as `d` was not Cohen’s d once a design had more
  than two conditions: the numerator was one pairwise contrast while the
  denominator pooled every condition, including ones outside the
  contrast. The recovery table now reports `margin` (points over the
  nearest competing condition), `nearest` (which condition that is), and
  `margin_d`, a genuine Cohen’s d for that pair. Naming the competitor
  also makes the number interpretable.
- [`rater_reliability()`](https://lobsterbush.github.io/repllm-docs/reference/rater_reliability.md)
  returns `icc_single` (ICC(A,1), one rater) and `icc_average`
  (ICC(A,k), their mean) instead of one unlabelled `icc`. On the bundled
  data these are .72 and .88 on the scientific dimension, so quoting the
  wrong one materially overstates agreement.
- [`rater_reliability()`](https://lobsterbush.github.io/repllm-docs/reference/rater_reliability.md)
  also returns `n_complete` and says so when the ICC used fewer
  materials than the data contains.
  [`irr::icc`](https://rdrr.io/pkg/irr/man/icc.html) drops any material
  a rater missed, so 10 percent of ratings failing can drop a third of
  materials, which the old output gave no way to see.
- `by_condition` reports `n_materials` alongside `n_ratings`. The single
  `n` counted ratings, so three materials rated by three raters read as
  `n = 9`.
- `se_type` no longer reports the first dimension’s estimator for all of
  them. Where dimensions differ it says so and exposes `se_types`.
- [`import_human_ratings()`](https://lobsterbush.github.io/repllm-docs/reference/import_human_ratings.md)
  distinguishes a cell a coder left blank from one holding something
  unreadable. A coder typing “five” previously became an
  indistinguishable NA in the blank count.
- `min_per_stratum` and the three `threshold` arguments are validated
  instead of failing inside the allocator or an `if`.

### Correctness fixes from adversarial testing

These came out of stress-testing every exported function against
degenerate, non-ASCII, and malformed input. Several of them would have
put a wrong number in a table without saying anything, which is the
worst way for a validation tool to fail.

- Non-ASCII studies had no protection at all.
  [`check_manipulation_leakage()`](https://lobsterbush.github.io/repllm-docs/reference/check_manipulation_leakage.md)
  and the system-prompt confound warning matched with PCRE `\b`, which
  is ASCII-only, so a French vignette containing “economique” (accented)
  in the condition of that name came back clean. Matching is
  Unicode-aware now, and terms in scripts without word separators (Han,
  Kana, Hangul, Thai) use substring matching, since boundary assertions
  can’t apply there.
- [`check_lexical_overlap()`](https://lobsterbush.github.io/repllm-docs/reference/check_lexical_overlap.md)
  tokenised on `[^a-z']`, splitting every accented word in two and
  producing empty vocabularies for non-Latin scripts. It now tokenises
  with Unicode semantics and reports rather than returning `[Inf, -Inf]`
  when nothing is comparable.
- [`check_readability()`](https://lobsterbush.github.io/repllm-docs/reference/check_readability.md)
  dropped conditions with no computable grade level from the spread,
  reporting a reassuring spread of zero and a PASS. Unmeasurable
  conditions are now named and fail the check.
- [`export_rating_task()`](https://lobsterbush.github.io/repllm-docs/reference/export_rating_task.md)
  could overwrite the blinding key with a rating sheet whenever `path`
  had no `.csv` suffix, which destroyed the only file linking materials
  to conditions. Paths are derived safely now, key and sheet paths are
  asserted distinct, and all the validation happens before anything gets
  written.
- A dimension named `rater`, `text`, `material_id`, or `condition`
  silently clobbered or renamed the sheet’s own columns; these names are
  now rejected.
- [`rater_reliability()`](https://lobsterbush.github.io/repllm-docs/reference/rater_reliability.md)
  fed duplicate material-by-rater rows to
  [`stats::reshape()`](https://rdrr.io/r/stats/reshape.html), which kept
  the first of each pair and returned ICC and alpha of 1.0 from
  contradictory ratings. Duplicates are now an error. All-missing
  ratings returned alpha 1.0 and now return `NA`.
- `.pooled_sd()` fell back to the overall standard deviation when no
  condition had within-condition variance, which pinned the reported
  standardised effect at a constant no matter what the data said. It
  returns `NA` now.
- `target` entries naming a condition absent from the ratings were
  dropped silently, shrinking the denominator so a partly-wrong target
  could report “recovered 2/2”. This is now an error.
- Factor and character `rating` columns crashed inside dplyr or produced
  `NA` means with a populated `sd`. Ratings are now validated, with
  character columns coerced when unambiguous and a clear error
  otherwise.
- `design_conditions(.exclude=)` returning `NA` replaced a real
  condition with an all-`NA` phantom row.
  [`replicate_design()`](https://lobsterbush.github.io/repllm-docs/reference/replicate_design.md)
  accepted `n = Inf` and silently overwrote an existing `replicate`
  column. Repeated factor levels now warn.
- [`validate_auto()`](https://lobsterbush.github.io/repllm-docs/reference/validate_auto.md)
  reported “all passed” while half the pool was unusable; the verdict
  now accounts for missing texts and unlabelled materials.
- [`synthetic_ratings()`](https://lobsterbush.github.io/repllm-docs/reference/synthetic_ratings.md)
  accepted `scale = c(NA, 7)` and failed inside `if()`.
- Rows with a missing condition label formed a silent `NA` stratum in
  the per-condition tables of the tier-1 checks. They are now excluded
  and counted in a new `n_no_condition` field.

### Dependencies

- `ellmer` minimum raised to 0.4.0, which is what the code has required
  since it began calling `parallel_chat()`.
- `purrr` and `jsonlite` dropped from Imports; `quanteda.textstats`
  added to Suggests.

## repllm 0.3.0

### New modules

- Response cleaning (`R/clean.R`): `clean_responses()` standardises raw
  LLM output (trim, lowercase, remap); `extract_label()` pulls the first
  matching category from verbose chain-of-thought responses;
  `label_distribution()` returns a frequency table with proportions.
- Visualisation (`R/visualize.R`): `plot_confusion()`,
  `plot_sensitivity()`, `plot_labels()`, `plot_cost()`:
  publication-ready ggplot figures using `theme_tufte()` when ggthemes
  is available.
- Material validation (`R/validate_materials.R`):
  [`check_length_balance()`](https://lobsterbush.github.io/repllm-docs/reference/check_length_balance.md),
  [`check_readability()`](https://lobsterbush.github.io/repllm-docs/reference/check_readability.md),
  `validate_materials()`: pre-deployment confound detection for
  experimental stimuli.

### New functions in existing modules

- [`generate_materials()`](https://lobsterbush.github.io/repllm-docs/reference/generate_materials.md):
  generate experimental stimuli from a factorial design matrix via LLM,
  with multiple versions per condition (Porter & Velez 2022).
- `code_structured()`: multi-dimensional text coding via LLM with JSON
  schema. Returns one column per coding dimension.
- `retry_failed()`: re-run only failed API calls and merge results back.
- `compare_runs()`: pairwise agreement between two executed runs.
- `session_cost_summary()`: cost breakdown by run and model from audit
  log.
- `sample_for_validation()` gains `stratify_by` for stratified sampling.

### Methodological improvements

- `downstream_sensitivity()` uses robust HC2 SEs via estimatr when
  available.
- `methods_section()` accepts `temperature` parameter; uses execution
  timestamp.
- `export_replication()` writes `parameters.json` instead of
  `parameters.txt`.
- `llm_run` objects now include `executed_at` timestamp.

### Internal improvements

- Added `.new_llm_run()` constructor with `.validate_llm_run()`
  validator.
- Replaced [`set.seed()`](https://rdrr.io/r/base/Random.html) with
  [`withr::with_seed()`](https://withr.r-lib.org/reference/with_seed.html)
  in exported functions.
- Switched from `%>%` to native `|>` pipe.
- `withr` moved to Imports; `estimatr` added to Suggests.

## repllm 0.2.0

### Major changes

- Rebuilt as a research methodology toolkit on top of `ellmer`. The
  package no longer reimplements LLM API calls. It uses `ellmer` for all
  LLM communication and focuses on experimental design, reliability,
  sensitivity analysis, validation, pre-registration, and reporting.

### New features

- Experimental design: `llm_run()`, `run_coding()`, `cross_design()`,
  [`replicate_design()`](https://lobsterbush.github.io/repllm-docs/reference/replicate_design.md),
  [`randomize_design()`](https://lobsterbush.github.io/repllm-docs/reference/randomize_design.md).
- Reliability: `llm_human_reliability()`,
  `llm_intermodel_reliability()`, `confusion_summary()`.
- Sensitivity: `prompt_sensitivity()`, `model_sensitivity()`,
  `downstream_sensitivity()`, `sensitivity_summary()`.
- Validation: `create_gold_standard()`, `sample_for_validation()`,
  `validate_against_gold()`.
- Pre-registration: `freeze_prompt()`, `verify_prompt()`,
  `export_preregistration()`, `load_frozen_prompt()`.
- Reporting: `methods_section()`, `results_to_latex()`,
  `export_replication()`, `estimate_cost()`.
- Logging: `log_run()`, `get_session_log()`, `export_audit_trail()`,
  `reset_session()`.

### Removed

- All provider-specific API functions. Use `ellmer` directly.

### Dependencies

- Added: `ellmer`, `jsonlite`.
- Removed: `httr`, `lubridate`.
- Moved `irr` to Suggests.
