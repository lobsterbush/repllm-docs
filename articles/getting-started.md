# Generating and validating experimental materials

## Why I wrote this

Say you’ve asked a model for sixteen vignettes crossing frame with
source, and they’ve come back, and they read well. A reviewer asks how
you know the economic ones are actually more economic than the moral
ones. That’s the question I couldn’t answer the first few times, and
it’s a fair one. Reading the vignettes and nodding isn’t a manipulation
check.

The awkward part is that you’re not only unsure whether the materials
carry the manipulation you asked for. You also don’t know what else they
picked up along the way. Length, reading level, and tone can all drift
with the condition, and any of them will turn up in your treatment
effect wearing your manipulation’s clothes.

The package grew out of Porter and Velez (2022). Their argument is that
picking one placebo, or one stimulus, hands the researcher more freedom
than anyone should want, and that you should generate a set and average
over it instead. I took that seriously and then noticed it doesn’t stop
at generation. If a model wrote the set, you didn’t choose any of them,
so you can’t vouch for what they do to a respondent either.

So the package generates the materials and then validates them in three
tiers. The first runs on your machine and costs nothing. It checks that
the conditions are comparable on length, reading level, and vocabulary,
and that no material announces its own condition. The second asks a
language model to rate every material, blind to condition, on the
construct you’re trying to move. The third does the same with human
coders on a subsample.

Tiers 2 and 3 ask the same question of different raters and report it in
the same shape, so you read the two results together. That’s what tells
you whether the model is seeing what your participants would see.

[ellmer](https://ellmer.tidyverse.org/) does all the model work here:
providers, credentials, concurrency, structured output, and cost
accounting.

## Designing and generating

Start with a factorial design. Each column becomes a placeholder
available to the generation template.

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

Generation needs an API key, so I haven’t run the next chunk.

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

Two things about that call. One `chat` object is shared across every
condition, so the system prompt stays constant and only the user turn
changes. If the system prompt named a condition itself, that framing
would land on every cell of the design, and I’ve made that mistake.
[`generate_materials()`](https://lobsterbush.github.io/repllm/reference/generate_materials.md)
now checks the system prompt against your factor levels and warns you.

Generating several realisations per condition rather than one follows
Porter and Velez (2022). Averaging over a few stimuli takes away the
freedom to pick the one you happen to like.

## Tier 1: automatic validation

The bundled `repllm_materials` dataset holds 24 generated vignettes
crossing three frames with two speaker types, so everything below runs
without a key.

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

The pool is balanced on length and reading level, and one check fails.
Each check is available on its own if you want the numbers underneath.

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

The leakage check is worth a word. A vignette in the economic condition
that contains the word “economic” lets a rater recover the condition
from the label instead of the content, so what you end up measuring is
label recognition.

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

Passing tier 1 doesn’t mean the manipulation worked. It means the
conditions are comparable on the nuisance dimensions, so if they differ
downstream, length and reading level aren’t the reason.

## Tier 2: synthetic validation

Now ask whether the manipulation moved the construct. Raters see the
text and nothing else. No condition labels, no generation prompts, and
the order is shuffled separately for each rater.

``` r
ratings <- synthetic_ratings(
  repllm_materials,
  dimensions = c(
    economic   = "how strongly the text appeals to economic consequences",
    moral      = "how strongly the text appeals to moral duty",
    scientific = "how strongly the text appeals to scientific evidence"
  ),
  chat     = ellmer::chat_openai(echo = "none"),
  n_raters = 3,
  personas = c("a general survey respondent",
               "a policy analyst",
               "an undergraduate student")
)
```

I’d supply `personas` if you’re running more than one rater. Without
them, and at a low temperature, your three synthetic raters are close to
the same rater three times over, and their agreement will look better
than it is.

[`synthetic_check()`](https://lobsterbush.github.io/repllm/reference/synthetic_check.md)
reports condition means with robust confidence intervals for every
dimension. When several raters rate the same material those ratings
aren’t independent, so the standard errors cluster by material.

The bundled `repllm_synthetic` dataset is what a run like that gives
you.

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

I’d report agreement among the synthetic raters alongside it, and say
which ICC you’re quoting. `icc_single` is the reliability of one rater,
`icc_average` the reliability of their mean, and on this data they
differ by up to .17.

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

The `target` argument names the dimension each condition was meant to
move. The recovery table says whether each condition actually scored
highest on its own dimension, and how big that gap is in pooled standard
deviations. The other dimensions are the discriminant check. A
manipulation that moves everything hasn’t isolated anything.

## Tier 3: human validation

Human coding is the expensive tier, so it runs on a subsample.
Allocation gives every condition at least one material before it
distributes the rest proportionally, so a rare cell never gets rounded
out of the sample.

``` r
subsample <- sample_for_human_validation(repllm_materials, n = 12,
                                         stratify_by = "frame", seed = 42)
#> ✔ Sampled 12 materials from 3 of 3 stratum/strata
table(subsample$frame)
#> 
#>   economic      moral scientific 
#>          4          4          4
```

[`export_rating_task()`](https://lobsterbush.github.io/repllm/reference/export_rating_task.md)
writes a blinded sheet for your coders and a separate key. The sheet has
no condition labels and its rows are shuffled. Keep the key away from
your coders.

``` r
task <- export_rating_task(
  subsample,
  path = file.path(tempdir(), "ratings.csv"),
  dimensions = c(economic = "economic appeal", moral = "moral appeal"),
  condition_col = "frame",
  n_raters = 2
)
#> ✔ Wrote 2 blinded rating sheets; key at /var/folders/hj/4jw7nfmx44q2c83zpn3h2n6m0000gq/T//RtmpobyqBS/ratings_key.csv
#> ℹ Rate each dimension from 1 to 7. Do not share the key with coders.
names(readr::read_csv(task$sheets[1], show_col_types = FALSE))
#> [1] "material_id" "text"        "rater"       "economic"    "moral"
```

When the sheets come back,
[`import_human_ratings()`](https://lobsterbush.github.io/repllm/reference/import_human_ratings.md)
rejoins the key and hands you the same long format the synthetic tier
produces, so the same analysis functions apply.

``` r
human <- import_human_ratings(task$sheets, task$key_path, scale = c(1, 7))
```

The bundled `repllm_human` dataset is what that gives you for these
materials.

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

On two of the three dimensions the human effects come out smaller than
the synthetic ones. On the third they don’t. Working out which is which
is the whole reason the next step exists.

## Reading the two tiers together

Both tiers came back above in the same shape, so set them next to each
other.

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

Both recover all three manipulations, so on either account the design
works. Each margin is measured against the nearest competing condition,
which the table names. The model reads the economic and moral margins as
wider than the coders do, and reads the scientific margin about the
same. Whether that gap between the tiers matters is a judgement about
your study, not something a threshold settles.

It’s also worth checking agreement material by material, which is a
different question from whether the condition means differ.

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

Around 0.9 on all three. The model orders the materials much as the
coders do, while seeing two of the three manipulations as larger than
they do. Those are separate facts and both are worth reporting.

An earlier version of this package folded all of that into one
pass-or-fail number. I took it out. The arithmetic mixed two things that
should stay apart: how much the tiers disagree about the materials, and
how much steadier a model rater is than a person. A quiet rater looked
inflated whether or not it disagreed with anyone, so the verdict was
partly measuring rater consistency and reporting it as a problem with
the stimuli.

## What this package doesn’t do

It doesn’t talk to any API, and it doesn’t manage credentials, retries,
concurrency, or cost accounting. ellmer already does all of that well,
and I’d rather point you at it than reimplement any of it. If you just
need to send text to a model from R, use ellmer directly. This package
is for the case where a model wrote your treatments and you have to
convince a reviewer that they worked.

## References

Porter, E., & Velez, Y. R. (2022). Placebo selection in survey
experiments: An agnostic approach. *Political Analysis*, 30(4), 481-494.
