# repllm

Research software · Charles Crabtree

Generate with a model.  
Validate with evidence.

An R toolkit for experimental materials: factorial designs, local
diagnostics, blinded model ratings, and human validation.

[Start with the data ↗](#quick-start) [Explore the reference
→](https://lobsterbush.github.io/repllm-docs/reference/index.html)

Version 0.4.0 · Experimental · Preparing for CRAN

------------------------------------------------------------------------

I wrote this package because I kept asking a language model to draft
vignettes for survey experiments, and then had no good answer when
someone asked whether the vignettes actually did what I said they did.

Generating stimuli this way is fast and it makes factorial designs
cheap. It also hands you a measurement problem that hand-written stimuli
didn’t have. You don’t know whether the materials carry the manipulation
you asked for, and you don’t know what else they carry along with it.
Length, reading level, tone, and vocabulary can all drift with the
condition, and any of those will show up in your treatment effect.

So `repllm` generates the materials and then validates them in three
tiers: local checks that cost nothing, a model rating every material
blind to condition, and human coders on a subsample. The last two ask
the same question of different raters, so you read them together.

------------------------------------------------------------------------

## Installation

`repllm` is not yet available on CRAN. The source repository currently
requires access from the maintainer. With repository access:

``` r
# install.packages("remotes")
remotes::install_github("lobsterbush/repllm")
```

If you have the source archive, install it and its dependencies with
`remotes::install_local("repllm_0.4.0.tar.gz", dependencies = TRUE)`.
Contact [Charles Crabtree](mailto:charles.crabtree@monash.edu) for
source access.

Requirements: R 4.1 or later. Local diagnostics and the bundled examples
need no credentials. Generation and synthetic rating require an
ellmer-supported provider and your own credentials; provider charges may
apply.

Documentation: <https://lobsterbush.github.io/repllm-docs/>

------------------------------------------------------------------------

## Three tiers of validation

01 / Automatic

### Inspect the materials

Check length, readability, condition leakage, and vocabulary locally.

02 / Synthetic

### Measure the manipulation

Ask model raters to score the text without condition labels or
generation history.

03 / Human

### Compare with people

Collect blinded human ratings on a stratified sample and compare the
evidence.

### The workflow

    design_conditions()  ->  generate_materials()
                                     |
                  +------------------+------------------+
                  |                  |                  |
            validate_auto()   synthetic_ratings()  sample_for_human_validation()
             (tier 1, free)    synthetic_check()      export_rating_task()
                                  (tier 2)            import_human_ratings()
                                                         human_check()
                                                          (tier 3)

    Tiers 2 and 3 ask the same question of different raters and report it the same
    way, so you set the two results side by side.

| Function | Purpose |
|----|----|
| [`design_conditions()`](https://lobsterbush.github.io/repllm-docs/reference/design_conditions.md) | Cross experimental factors into a design matrix |
| [`replicate_design()`](https://lobsterbush.github.io/repllm-docs/reference/replicate_design.md) / [`randomize_design()`](https://lobsterbush.github.io/repllm-docs/reference/randomize_design.md) | Several realisations per condition; shuffle order |
| [`generate_materials()`](https://lobsterbush.github.io/repllm-docs/reference/generate_materials.md) | Write the stimuli from the design via ellmer |
| [`generation_sensitivity()`](https://lobsterbush.github.io/repllm-docs/reference/generation_sensitivity.md) | Run the same design under several generators |
| [`validate_auto()`](https://lobsterbush.github.io/repllm-docs/reference/validate_auto.md) | Tier 1: all the local checks at once |
| [`check_length_balance()`](https://lobsterbush.github.io/repllm-docs/reference/check_length_balance.md) | Word and character counts across conditions |
| [`check_readability()`](https://lobsterbush.github.io/repllm-docs/reference/check_readability.md) | Flesch-Kincaid grade level across conditions |
| [`check_manipulation_leakage()`](https://lobsterbush.github.io/repllm-docs/reference/check_manipulation_leakage.md) | Materials that name their own condition |
| [`check_lexical_overlap()`](https://lobsterbush.github.io/repllm-docs/reference/check_lexical_overlap.md) | Whether conditions are too alike or too different |
| [`synthetic_ratings()`](https://lobsterbush.github.io/repllm-docs/reference/synthetic_ratings.md) | Tier 2: blind LLM ratings of every material |
| [`synthetic_check()`](https://lobsterbush.github.io/repllm-docs/reference/synthetic_check.md) | Did the manipulation move the target construct? |
| [`rater_reliability()`](https://lobsterbush.github.io/repllm-docs/reference/rater_reliability.md) | Agreement among raters |
| [`sample_for_human_validation()`](https://lobsterbush.github.io/repllm-docs/reference/sample_for_human_validation.md) | Tier 3: stratified subsample for human coding |
| [`export_rating_task()`](https://lobsterbush.github.io/repllm-docs/reference/export_rating_task.md) | Blinded rating sheets plus a separate key |
| [`import_human_ratings()`](https://lobsterbush.github.io/repllm-docs/reference/import_human_ratings.md) | Read the completed sheets back into long format |
| [`human_check()`](https://lobsterbush.github.io/repllm-docs/reference/human_check.md) / [`human_reliability()`](https://lobsterbush.github.io/repllm-docs/reference/human_reliability.md) | The tier-3 counterparts |

The package ships one simulated study so you can run all of this without
an API key: `repllm_materials` holds 24 generated vignettes, and
`repllm_synthetic` and `repllm_human` hold ratings of them by three LLM
raters and two human coders.

------------------------------------------------------------------------

## Quick start

Run a complete local check before connecting a provider:

``` r
library(repllm)
validate_auto(repllm_materials, condition_col = "frame")
targets <- c(economic = "economic", moral = "moral", scientific = "scientific")
synthetic_check(repllm_synthetic, target = targets)
human_check(repllm_human, target = targets)
```

The bundled materials and ratings are simulated teaching data, not
evidence that model ratings reproduce human judgments in a real study.

### Generate your own materials

``` r
library(repllm)

design <- design_conditions(
  frame  = c("economic", "moral"),
  source = c("expert", "layperson")
)

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

One `chat` object is shared across every condition, so the system prompt
stays constant and only the user turn varies. If the system prompt named
a condition itself, that framing would land on every cell of the design,
and I’ve made that mistake.
[`generate_materials()`](https://lobsterbush.github.io/repllm-docs/reference/generate_materials.md)
now checks the system prompt against your factor levels and warns you.

Generating three versions per condition rather than one follows Porter
and Velez (2022). Averaging over several stimuli takes away the freedom
to pick the one you happen to like.

### Tier 1: the local checks

These run on your machine, cost nothing, and need no key.

``` r
data(repllm_materials)
validate_auto(repllm_materials, condition_col = "frame")
#> -- Automatic validation --
#> v Length balance: max deviation 4.8%
#> v Readability: grade-level spread 0.4
#> ! Manipulation leakage: 2/24 materials name a condition term.
#> v Distinctiveness: Jaccard overlap in [0.1, 0.11]
#> ! Needs attention: "leakage"
```

Two of the 24 vignettes use the word “economic” or “scientific” inside
the condition of that name. That lets a rater recover the condition from
the label instead of the content, so what you’d be measuring is label
recognition.

Passing tier 1 means these diagnostics found no differences beyond the
chosen thresholds. It does not establish equivalence or rule out other
confounds. Readability scores use an English-language formula; inspect
non-English materials with language-appropriate measures.

### Tier 2: synthetic raters

``` r
ratings <- synthetic_ratings(
  repllm_materials,
  dimensions = c(
    economic = "how strongly the text appeals to economic consequences",
    moral    = "how strongly the text appeals to moral duty"
  ),
  chat     = ellmer::chat_openai(echo = "none"),
  condition_col = "frame",
  n_raters = 3,
  personas = c("a general survey respondent", "a policy analyst",
               "an undergraduate student")
)

targets <- c(economic = "economic", moral = "moral", scientific = "scientific")
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

Each row is the margin over the nearest competing condition, named, with
Cohen’s d for that pair. Reporting the margin against the strongest
competitor rather than against an average is the stricter test, and
naming the competitor keeps the d interpretable.

Raters see the text and nothing else. No condition labels, no generation
prompts, and the order is shuffled separately for each rater. Ratings
come back through
[`ellmer::parallel_chat_structured()`](https://ellmer.tidyverse.org/reference/parallel_chat.html)
with a `type_object()` schema, so the model can’t hand you a value off
the scale and there’s nothing to parse.

I’d supply `personas` if you’re running more than one rater. Without
them, and at a low temperature, your three synthetic raters are close to
the same rater three times over, and their agreement will look better
than it is.

### Tier 3: human coders

``` r
dims <- c(economic   = "economic appeal",
          moral      = "moral appeal",
          scientific = "scientific appeal")

sub  <- sample_for_human_validation(repllm_materials, n = 12,
                                    stratify_by = "frame")
task <- export_rating_task(sub, file.path(tempdir(), "sheet.csv"),
                           dimensions = dims, condition_col = "frame",
                           n_raters = 3)

# ... your coders fill in the sheets ...

human <- import_human_ratings(task$sheets, task$key_path, scale = c(1, 7))
human_check(human, target = targets)
```

The sheets carry no condition labels and their rows are shuffled. The
key goes to a separate file so it never reaches your coders.

### Reading the two tiers together

Both tiers report the same thing in the same shape, so put them next to
each other. On the bundled example:

``` r
targets <- c(economic = "economic", moral = "moral", scientific = "scientific")
synthetic_check(repllm_synthetic, target = targets)
#>     ok    economic     on economic     margin +3.71 over scientific   (d = +6.23)
#>     ok    moral        on moral        margin +3.37 over scientific   (d = +6.67)
#>     ok    scientific   on scientific   margin +2.75 over economic     (d = +3.23)

human_check(repllm_human, target = targets)
#>     ok    economic     on economic     margin +2.44 over scientific   (d = +2.82)
#>     ok    moral        on moral        margin +2.62 over scientific   (d = +3.24)
#>     ok    scientific   on scientific   margin +2.62 over economic     (d = +3.16)
```

In this simulated example, both tiers rank each intended condition
highest. That descriptive ordering is not a significance test or
evidence of equivalence. The model reads the economic and moral margins
as wider than your coders do, and reads the scientific margin about the
same. That is worth looking at before you decide the manipulation is
strong.

[`rater_reliability()`](https://lobsterbush.github.io/repllm-docs/reference/rater_reliability.md)
is worth reporting beside it, and note which ICC you quote. On this data
one synthetic rater is .72 to .94 depending on dimension, while the mean
of three is .88 to .98. Those are different claims.

I would not reduce this to a single pass or fail number. An earlier
version of the package did, and the arithmetic turned out to mix two
things that should not be mixed: how much the tiers actually disagree
about the materials, and how much steadier a model rater is than a
person. A quiet rater came out looking inflated whether or not it
disagreed with anyone. Read the two tables.

## What this owes to Porter and Velez

The package grew out of Porter and Velez (2022). Their argument is that
picking one placebo, or one stimulus, hands the researcher more freedom
than anyone should want, and that you should generate a set and average
over it instead.

I took that seriously and then noticed it doesn’t stop at generation. If
a model wrote the set, you didn’t choose any of them, and you can’t
vouch for what they do to a respondent. So the generation has to arrive
with measurement attached, and that’s what the three tiers here are.
[`replicate_design()`](https://lobsterbush.github.io/repllm-docs/reference/replicate_design.md)
and the `n_versions` argument are their idea directly. The rest is what
I think follows from it.

Porter, E., & Velez, Y. R. (2022). Placebo selection in survey
experiments: An agnostic approach. *Political Analysis*, 30(4), 481-494.

## Citation

``` bibtex
@software{repllm2026,
  title  = {repllm: Generation and Validation of LLM-Written Experimental Materials},
  author = {Crabtree, Charles},
  year   = {2026},
  url    = {https://github.com/lobsterbush/repllm},
  note   = {R package version 0.4.0}
}
```

------------------------------------------------------------------------

## Contact

The [issue tracker](https://github.com/lobsterbush/repllm-docs/issues)
is the best place for bugs and requests, or email me at
<charles.crabtree@monash.edu>. I’d be glad to hear what breaks.

Charles Crabtree, Monash University and Korea University. MIT License.

## Provenance

**AI – Human (editor) 🤖✏️👤**

Declared by Charles Crabtree: AI produced the work, with Charles
Crabtree as human editor and package maintainer. This declaration covers
the package and its documentation. The September 2026 audit and site
revision used OpenAI Codex.

The label follows [The Latent Review’s provenance
standard](https://thelatentreview.com/provenance/), shared under [CC BY
4.0](https://creativecommons.org/licenses/by/4.0/). The package remains
MIT licensed. This authorship declaration is distinct from the model,
prompt, and timestamp provenance recorded for generated materials.

## Development and replication

For development, install `devtools`, `here`, and `pkgdown` from CRAN.
From a local source checkout, run
`remotes::install_deps(dependencies = TRUE)`, then:

``` r
devtools::test()
devtools::check(args = "--as-cran")
source(here::here("data-raw", "build_site.R"))
```

The tests mock provider calls and require no API keys. The
getting-started vignette reproduces the local validation examples from
the bundled data.
