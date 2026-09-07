# Collect model ratings of the materials

Ask a model to rate the texts on the dimensions you specify. I'd use
these ratings to screen a pool of materials before comparing them with
human ratings on a sample.
[`human_check`](https://lobsterbush.github.io/repllm-docs/reference/human_check.md)
uses the same analysis as
[`synthetic_check`](https://lobsterbush.github.io/repllm-docs/reference/synthetic_check.md).

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
  [`generate_materials`](https://lobsterbush.github.io/repllm-docs/reference/generate_materials.md).

- dimensions:

  Named character vector or list. Names become dimension names; values
  describe what the rater should judge, e.g.
  `c(economic = "how strongly the text appeals to economic consequences")`.

- chat:

  An ellmer Chat object. Its system prompt is replaced with the
  generated rating instruction and its conversation history is cleared
  on a clone. The supplied chat is not modified.

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
dropped. Materials without usable text are skipped; their count is
stored in `n_missing_materials`.

## What the raters see

The package sends each material's text without its condition label,
generation prompt, or design columns. It clears earlier chat turns on a
clone, replaces the system prompt with the rating instructions, and
shuffles the texts for each rater. The stimulus itself can still give
away the condition.

## See also

[`synthetic_check`](https://lobsterbush.github.io/repllm-docs/reference/synthetic_check.md),
[`rater_reliability`](https://lobsterbush.github.io/repllm-docs/reference/rater_reliability.md)

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
