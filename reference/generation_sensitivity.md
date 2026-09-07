# Generate the Same Design Under Several Models

Re-run stimulus generation with more than one generator and stack the
results. It's the treatment-side version of a sensitivity analysis. If
the manipulation only survives under one model, then what you have is a
property of that model rather than of your design.

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
  [`generate_materials`](https://lobsterbush.github.io/repllm/reference/generate_materials.md).

## Value

A tibble of class `llm_materials` with an added `generator` column, and
one `"provenance"` entry per generator.

## Details

To compare generators, split the result and run the checks on each
piece:
`lapply(split(m, m$generator), validate_auto, condition_col = "frame")`.
If a check passes under one generator and fails under another, the
design isn't doing the work you think it is.

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
