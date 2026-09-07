# Rate Materials with Synthetic (LLM) Raters

Ask a model to rate every material on one or more dimensions, blind to
the condition that produced it. This is the second tier. It's cheap
enough to run on the whole stimulus pool, and it asks whether the
manipulation moved the construct you meant it to move.

## Usage

``` r
synthetic_ratings(
  materials,
  dimensions,
  chat,
  n_raters = 3L,
  personas = NULL,
  scale = c(1, 7),
  condition_col = "condition",
  text_col = "text",
  seed = NULL,
  max_active = 10,
  rpm = 500
)
```

## Arguments

- materials:

  A data frame of materials, typically from
  [`generate_materials`](https://lobsterbush.github.io/repllm/reference/generate_materials.md).

- dimensions:

  Named character vector or list. Names become dimension names; values
  describe what the rater should judge, e.g.
  `c(economic = "how strongly the text appeals to economic consequences")`.

- chat:

  An ellmer Chat object. Its system prompt is replaced with the
  generated rating instruction, so pass a bare chat.

- n_raters:

  Number of independent synthetic raters (default: 3).

- personas:

  Optional character vector of rater perspectives, recycled to
  `n_raters`. Without them, and at a low temperature, your synthetic
  raters are close to the same rater several times over and their
  agreement will look better than it is.

- scale:

  Numeric length-2 vector giving the rating endpoints (default:
  `c(1, 7)`).

- condition_col:

  Column identifying the condition (default: `"condition"`). Used only
  for labelling the output, never sent.

- text_col:

  Column holding the material text (default: `"text"`).

- seed:

  Optional integer seed for the presentation-order shuffle.

- max_active, rpm:

  Concurrency controls passed to ellmer.

## Value

A long tibble with `material_id`, `condition`, `rater`, `dimension`, and
`rating`. Failed or invalid ratings are kept as `NA` rather than
dropped.

## Details

Synthetic ratings are evidence rather than proof. I'd treat them as a
screening instrument. Rate a subsample by hand with
[`human_check`](https://lobsterbush.github.io/repllm/reference/human_check.md)
and compare the two before you rely on the model's ratings for the rest
of the pool.

## Blinding

Raters see the material text and nothing else. Condition labels,
generation prompts, and design columns never get sent. The order is
shuffled separately for each rater, so position can't track condition.

## See also

[`synthetic_check`](https://lobsterbush.github.io/repllm/reference/synthetic_check.md),
[`rater_reliability`](https://lobsterbush.github.io/repllm/reference/rater_reliability.md)

## Examples

``` r
if (FALSE) { # \dontrun{
ratings <- synthetic_ratings(
  materials,
  dimensions = c(
    economic = "how strongly the text appeals to economic consequences",
    moral    = "how strongly the text appeals to moral duty"
  ),
  chat = ellmer::chat_openai(echo = "none"),
  n_raters = 3,
  personas = c("a general survey respondent",
               "a policy analyst",
               "an undergraduate student")
)
} # }
```
