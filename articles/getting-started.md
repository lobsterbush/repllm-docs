# Generating and validating experimental materials

## Why I wrote this

How would I know whether model-written vignettes convey the manipulation
I asked for? That’s the question behind this package. A text can read
well and still give respondents something different from what I
intended.

I might ask for an economic and a moral argument about the same policy.
The model might also make one longer, more emotional, or harder to read.
I want to check those differences before interpreting an experimental
result.

The package grew out of Porter and Velez (2022). Their work motivates
using several stimuli per condition. I’ve added tools for inspecting
those stimuli and collecting ratings of what they convey.

I’ll start with checks I can run on the text itself, then compare model
ratings with ratings in the human-coder format. The rating data in this
vignette are simulated. I’ve included them so you can run the analysis
without an API key.

[ellmer](https://ellmer.tidyverse.org/) handles the model calls and
credentials.

## Designing and generating

First, name the factors and their levels. Here I cross the argument’s
frame with the type of person making it. Each column becomes a
placeholder in the prompt.

``` r
design <- design_conditions(
  frame  = c("economic", "moral"),
  source = c("expert", "layperson")
)
#> ℹ Design: 4 conditions from 2 factors
design
#> # A tibble: 4 × 3
#>   condition_id frame    source   
#>          <int> <chr>    <chr>    
#> 1            1 economic expert   
#> 2            2 moral    expert   
#> 3            3 economic layperson
#> 4            4 moral    layperson
```

The next example needs provider credentials, so it isn’t run when this
vignette is built.

``` r
chat <- ellmer::chat_openai(
  "You write short, neutral survey vignettes. Return only the vignette text.",
  echo = "none"
)

materials <- generate_materials(
  design,
  template = "Write a 60-word quote about a carbon tax from a {source}
              emphasising the {frame} perspective.",
  chat = chat,
  n_versions = 3
)
```

I keep the system prompt general because it applies to every condition.
The condition-specific instructions go in `template`. Use a fresh chat:
[`generate_materials()`](https://lobsterbush.github.io/repllm-docs/reference/generate_materials.md)
rejects earlier conversation turns and warns if the system prompt names
a factor level.

The multiple versions follow Porter and Velez (2022). I want to avoid
making the result depend on one particular stimulus.

## Tier 1: automatic validation

For the rest of the analysis, I’ll use the bundled carbon-tax texts.
There are 24: three frames crossed with two speaker types, with four
versions per cell.

``` r
data(repllm_materials)
auto <- validate_auto(repllm_materials, condition_col = "frame")
#> 
#> ── Automatic validation ──
#> 
#> ✔ Length balance: max deviation 4.8%
#> ✔ Readability: grade-level spread 0.4
#> ! Manipulation leakage: 2/24 materials name a condition term.
#> ✔ Distinctiveness: Jaccard overlap in [0.1, 0.11]
#> ! Needs attention: "leakage"
auto
#> Automatic validation
#>   Materials: 24 across 3 conditions
#>   length          PASS
#>   readability     PASS
#>   leakage         WARNING
#>   distinctiveness PASS
#>   Overall: needs attention
```

Length and readability pass the default thresholds, but the leakage
check flags two texts. I can also run each check separately to inspect
its results.

``` r
check_length_balance(repllm_materials$text, repllm_materials$frame)$by_condition
#> ✔ Length balance: max deviation 4.8%
#> # A tibble: 3 × 6
#>   condition      n mean_words sd_words mean_chars deviation
#>   <chr>      <int>      <dbl>    <dbl>      <dbl>     <dbl>
#> 1 economic       8       42.4     2.83       240.    0.0180
#> 2 moral          8       42.9     2.42       248.    0.0300
#> 3 scientific     8       39.6     2.97       236.    0.0480
```

The leakage check finds words that could give away the condition. If a
text calls its own argument “economic”, a rater could score that label
rather than the argument. I’d read each flagged text before deciding
what to change.

``` r
flagged <- check_manipulation_leakage(repllm_materials$text,
                                      repllm_materials$frame)$flagged
#> ! Manipulation leakage: 2/24 materials name a condition term.
flagged[, c("condition", "term")]
#> # A tibble: 2 × 2
#>   condition  term      
#>   <chr>      <chr>     
#> 1 economic   economic  
#> 2 scientific scientific
```

These checks help me find obvious problems. Passing them doesn’t
establish that the materials are equivalent on everything except the
manipulation. The readability formula is designed for English; other
languages need an appropriate measure.

## Tier 2: synthetic validation

Next, I ask a model to rate how much each text appeals to economic
consequences, moral duty, and scientific evidence. The package supplies
the text without its condition label or generation prompt. It clears
earlier chat turns and shuffles the order separately for each rater.

``` r
ratings <- synthetic_ratings(
  repllm_materials,
  dimensions = c(
    economic   = "how strongly the text appeals to economic consequences",
    moral      = "how strongly the text appeals to moral duty",
    scientific = "how strongly the text appeals to scientific evidence"
  ),
  chat     = ellmer::chat_openai(echo = "none"),
  condition_col = "frame",
  n_raters = 3,
  personas = c("a general survey respondent",
               "a policy analyst",
               "an undergraduate student")
)
```

I’ve supplied different `personas` here. They may help vary the ratings,
but they don’t turn repeated calls to one model into independent human
judgments.

[`synthetic_check()`](https://lobsterbush.github.io/repllm-docs/reference/synthetic_check.md)
reports means by condition and contrasts with robust confidence
intervals. When several raters score a material, the standard errors
cluster by material.

I’ll use the simulated `repllm_synthetic` ratings to show the output:

``` r
data(repllm_synthetic)

targets <- c(economic = "economic", moral = "moral",
             scientific = "scientific")

synthetic_check(repllm_synthetic, target = targets)
#> Synthetic validation
#>   Materials: 24 | raters: 3 | dimensions: 3 
#>   Ratings: 216
#>   Standard errors: cluster-robust by material (24 clusters) 
#>   Manipulation recovery:
#>     ok    economic     on economic     margin +3.71 over scientific   (d = +6.23)
#>     ok    moral        on moral        margin +3.37 over scientific   (d = +6.67)
#>     ok    scientific   on scientific   margin +2.75 over economic     (d = +3.23)
#>   Recovered 3/3 intended contrasts
```

I’d report rater agreement alongside the condition comparisons.
`icc_single` is the reliability of one rater; `icc_average` is the
reliability of their mean. They differ by up to .17 in these data, so
I’d be explicit about which one I’m quoting.

``` r
rater_reliability(repllm_synthetic)
#> # A tibble: 3 × 8
#>   dimension  n_raters n_materials n_complete n_failed icc_single icc_average
#>   <chr>         <int>       <int>      <int>    <int>      <dbl>       <dbl>
#> 1 economic          3          24         24        0      0.935       0.977
#> 2 moral             3          24         24        0      0.930       0.975
#> 3 scientific        3          24         24        0      0.716       0.883
#> # ℹ 1 more variable: krippendorff_alpha <dbl>
```

The `target` argument maps each condition to the dimension it’s meant to
move. The recovery table reports whether that condition has the highest
mean and its gap over the strongest competitor. I also inspect the other
dimensions. A treatment may change more than I intended.

## Tier 3: human validation

I’d usually start human coding with a stratified sample. By default, the
sampler allocates at least one material per condition when the requested
sample is large enough, then distributes the rest proportionally. It
reports conditions that couldn’t be included.

``` r
subsample <- sample_for_human_validation(repllm_materials, n = 12,
                                         stratify_by = "frame", seed = 42)
#> ✔ Sampled 12 materials from 3 of 3 stratum/strata
table(subsample$frame)
#> 
#>   economic      moral scientific 
#>          4          4          4
```

Now I’ll prepare the rating sheets. Each coder gets a shuffled copy with
random IDs. The separate key holds the original IDs and condition
labels. Keep that file away from the coders; use new paths if you’re
exporting another task.

``` r
task <- export_rating_task(
  subsample,
  path = file.path(tempdir(), "ratings.csv"),
  dimensions = c(economic = "economic appeal", moral = "moral appeal"),
  condition_col = "frame",
  n_raters = 2
)
#> ✔ Wrote 2 blinded rating sheets; key at /var/folders/hj/4jw7nfmx44q2c83zpn3h2n6m0000gq/T//Rtmpp3qciS/ratings_key.csv
#> ℹ Rate each dimension from 1 to 7. Do not share the key with coders.
names(readr::read_csv(task$sheets[1], show_col_types = FALSE))
#> [1] "material_id" "text"        "rater"       "economic"    "moral"
```

Once people have filled in the sheets, import their ratings with the
key. The function restores the original IDs and produces the same long
format used for model ratings.

``` r
human <- import_human_ratings(task$sheets, task$key_path, scale = c(1, 7))
```

For the example below, I’ll use `repllm_human`. These are simulated
human ratings of all 24 materials, so they aren’t the completed sheets
from the 12-material sample above.

``` r
data(repllm_human)

human_check(repllm_human, target = targets)
#> Human validation
#>   Materials: 24 | raters: 2 | dimensions: 3 
#>   Ratings: 144
#>   Standard errors: cluster-robust by material (24 clusters) 
#>   Manipulation recovery:
#>     ok    economic     on economic     margin +2.44 over scientific   (d = +2.82)
#>     ok    moral        on moral        margin +2.62 over scientific   (d = +3.24)
#>     ok    scientific   on scientific   margin +2.62 over economic     (d = +3.16)
#>   Recovered 3/3 intended contrasts
human_reliability(repllm_human)
#> # A tibble: 3 × 8
#>   dimension  n_raters n_materials n_complete n_failed icc_single icc_average
#>   <chr>         <int>       <int>      <int>    <int>      <dbl>       <dbl>
#> 1 economic          2          24         24        0      0.753       0.859
#> 2 moral             2          24         24        0      0.754       0.860
#> 3 scientific        2          24         24        0      0.794       0.885
#> # ℹ 1 more variable: krippendorff_alpha <dbl>
```

The human and model ratings give somewhat different accounts of the
materials. I’d inspect the gaps directly before interpreting that
difference.

## Reading the two tiers together

Here I put the recovery margins next to each other:

``` r
syn <- synthetic_check(repllm_synthetic, target = targets)$recovery
hum <- human_check(repllm_human, target = targets)$recovery

data.frame(
  condition = syn$condition,
  nearest   = syn$nearest,
  synthetic = round(syn$margin, 2),
  human     = round(hum$margin, 2)
)
#>    condition    nearest synthetic human
#> 1   economic scientific      3.71  2.44
#> 2      moral scientific      3.37  2.62
#> 3 scientific   economic      2.75  2.62
```

Both sets of ratings put each intended condition highest on its target
dimension. The economic and moral gaps are wider in the model ratings;
the scientific gaps are similar. That ranking doesn’t establish that the
design works with respondents, or that the two sets of ratings are
interchangeable.

I’d also compare ratings of the same materials. Agreement in how raters
order individual texts is a different question from the size of the
condition gaps.

``` r
a <- aggregate(rating ~ material_id + dimension, repllm_synthetic, mean)
b <- aggregate(rating ~ material_id + dimension, repllm_human, mean)
m <- merge(a, b, by = c("material_id", "dimension"),
           suffixes = c("_syn", "_hum"))

sapply(split(m, m$dimension),
       function(d) round(cor(d$rating_syn, d$rating_hum), 2))
#>   economic      moral scientific 
#>       0.90       0.92       0.91
```

The correlations are around 0.9 on each dimension. In these simulated
data, the model and human ratings order the materials similarly even
though some condition gaps differ. I’d report both findings.

I’d report condition differences alongside agreement on individual
materials. Those answer different questions. A single pass-or-fail score
would hide which part of the comparison needs attention.

## Using repllm with ellmer

I use ellmer for provider calls, credentials, retries, and cost
accounting. If you want to send text to a model from R, ellmer can do
that directly. `repllm` adds the experimental design and validation
steps described here.

## References

Porter, E., & Velez, Y. R. (2022). Placebo selection in survey
experiments: An agnostic approach. *Political Analysis*, 30(4), 481-494.
