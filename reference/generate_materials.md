# Generate materials for each condition

Ask a model to write experimental materials from your design. Each row
produces `n_versions` separate responses, following Porter and Velez
(2022). You can use this for vignettes, quotations, or other short
texts.

## Usage

``` r
generate_materials(
  design,
  template,
  chat,
  n_versions = 3L,
  seed = NULL,
  max_active = 10,
  rpm = 500
)
```

## Arguments

- design:

  A data frame of conditions, typically from
  [`design_conditions`](https://lobsterbush.github.io/repllm-docs/reference/design_conditions.md).
  Column names become `{placeholder}`s in `template`.

- template:

  A [`glue`](https://glue.tidyverse.org/reference/glue.html)-style
  string used to build the per-condition instruction. Column names from
  `design` are available as `{name}`.

- chat:

  An ellmer Chat object used for every condition. Its system prompt
  should carry only task-general instructions (length, register,
  format), never condition-specific content. Use a fresh chat with no
  conversation history; previously used chats are rejected.

- n_versions:

  Number of independent realisations per condition (default: 3).

- seed:

  Optional integer seed recorded in the provenance attributes. Note that
  most providers are not bit-reproducible even at temperature 0.

- max_active:

  Maximum concurrent requests.

- rpm:

  Requests per minute limit.

## Value

A tibble of class `llm_materials`: all columns of `design`, plus
`material_id`, `version`, `prompt`, and `text`. Failed generations keep
their row with `text = NA`. Generation provenance is stored in the
`"provenance"` attribute.

## Keep the system prompt general

The same system prompt applies to every condition. I put instructions
about length and format there, and condition-specific wording in the
template. `generate_materials()` warns if the system prompt names a
factor level. Use a fresh chat so earlier conversation turns don't enter
the requests.

## References

Porter, E., & Velez, Y. R. (2022). Placebo selection in survey
experiments: An agnostic approach. *Political Analysis*, 30(4), 481-494.

## Examples

``` r
if (FALSE) { # \dontrun{
design <- design_conditions(
  frame  = c("economic", "moral"),
  source = c("expert", "layperson")
)

chat <- ellmer::chat_openai(
  "You write short, neutral survey vignettes. Return only the vignette.",
  echo = "none"
)

materials <- generate_materials(
  design,
  template = "Write a 60-word quote about a carbon tax from a {source}
              emphasising the {frame} perspective.",
  chat = chat,
  n_versions = 3
)
} # }
```
