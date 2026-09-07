# Generate materials with several models

Run the same design and template with several generators and combine
their outputs. I'd use this to see whether the materials depend heavily
on which model writes them.

## Usage

``` r
generation_sensitivity(design, template, chats, n_versions = 3L, ...)
```

## Arguments

- design:

  A data frame of conditions.

- template:

  A glue-style instruction template.

- chats:

  A named list of ellmer Chat objects, one per generator.

- n_versions:

  Realisations per condition per generator (default: 3).

- ...:

  Passed to
  [`generate_materials`](https://lobsterbush.github.io/repllm-docs/reference/generate_materials.md).

## Value

A tibble of class `llm_materials` with an added `generator` column, and
one `"provenance"` entry per generator.

## Details

To compare the results, split them by generator and run the checks
separately:
`lapply(split(m, m$generator), validate_auto, condition_col = "frame")`.
If the checks differ across models, inspect the texts before deciding
what to field.

## Examples

``` r
if (FALSE) { # \dontrun{
materials <- generation_sensitivity(
  design, template,
  chats = list(
    gpt   = ellmer::chat_openai(sys, echo = "none"),
    claude = ellmer::chat_anthropic(sys, echo = "none")
  )
)
} # }
```
