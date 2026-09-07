# Run the local text checks

Check length, reading level, vocabulary, and words that could give away
the condition. These checks run locally and don't need an API key.

## Usage

``` r
validate_auto(
  materials,
  condition_col = "condition",
  text_col = "text",
  length_threshold = 0.2,
  readability_threshold = 2,
  leakage_terms = NULL,
  custom_checks = NULL
)
```

## Arguments

- materials:

  A data frame of materials, typically from
  [`generate_materials`](https://lobsterbush.github.io/repllm-docs/reference/generate_materials.md).

- condition_col:

  Name of the column holding condition labels (default: `"condition"`).
  For a multi-factor design, pass the factor you want conditions
  compared on.

- text_col:

  Name of the text column (default: `"text"`).

- length_threshold:

  Proportional deviation from the grand mean word count that triggers a
  warning (default: 0.20).

- readability_threshold:

  Maximum acceptable grade-level spread between conditions (default:
  2.0).

- leakage_terms:

  Optional character vector of terms that should not appear in the
  materials. Defaults to the unique condition labels.

- custom_checks:

  Optional named list of functions. Each takes
  `(materials, condition_col, text_col)` and returns a list with at
  least a logical `pass`.

## Value

An object of class `auto_validation`.

## Details

I start with these checks before collecting ratings. Passing the checks
means the materials met the chosen thresholds. It doesn't rule out other
confounds or establish that the manipulation works. Use
[`synthetic_check`](https://lobsterbush.github.io/repllm-docs/reference/synthetic_check.md)
and
[`human_check`](https://lobsterbush.github.io/repllm-docs/reference/human_check.md)
to examine ratings of what the texts convey.

## See also

[`synthetic_check`](https://lobsterbush.github.io/repllm-docs/reference/synthetic_check.md),
[`human_check`](https://lobsterbush.github.io/repllm-docs/reference/human_check.md)

## Examples

``` r
m <- data.frame(
  condition = c("economic", "economic", "moral", "moral"),
  text = c(
    "The tax creates 50,000 jobs and raises GDP by 2.3 percent.",
    "Firms would gain steady revenue and hire more staff next year.",
    "We owe future generations a livable world and must act now.",
    "Protecting our children from harm is a duty we cannot ignore."
  )
)
validate_auto(m)
#> 
#> ── Automatic validation ──
#> 
#> ✔ Length balance: max deviation 0%
#> ! Readability: grade-level spread 2.1 (> 2)
#> ✔ Manipulation leakage: none detected.
#> ✔ Distinctiveness: Jaccard overlap in [0.03, 0.03]
#> ℹ Conditions share little vocabulary (min Jaccard 0.03); check that they differ only on the intended dimension.
#> ! Needs attention: "readability"
#> Automatic validation
#>   Materials: 4 across 2 conditions
#>   length          PASS
#>   readability     WARNING
#>   leakage         PASS
#>   distinctiveness PASS
#>   Overall: needs attention 
```
