# Example experimental materials

I've included 24 carbon-tax vignettes: three frames crossed with two
speaker types, with four versions per cell. They form a worked example
with the simulated ratings in
[repllm_synthetic](https://lobsterbush.github.io/repllm-docs/reference/repllm_synthetic.md)
and
[repllm_human](https://lobsterbush.github.io/repllm-docs/reference/repllm_human.md).

## Usage

``` r
repllm_materials
```

## Format

A tibble with 24 rows and 5 columns:

- material_id:

  Identifier, `"m01"` to `"m24"`.

- frame:

  Framing condition: `"economic"`, `"moral"`, or `"scientific"`.

- source:

  Speaker condition: `"expert"` or `"citizen"`.

- version:

  Realisation number within the condition, 1 to 4.

- text:

  The generated vignette.

## Details

Two texts contain their own condition labels, so
[`check_manipulation_leakage`](https://lobsterbush.github.io/repllm-docs/reference/check_manipulation_leakage.md)
flags them. Length and readability pass the default thresholds. These
data let you inspect the checks without making a model call.

## See also

[repllm_synthetic](https://lobsterbush.github.io/repllm-docs/reference/repllm_synthetic.md),
[repllm_human](https://lobsterbush.github.io/repllm-docs/reference/repllm_human.md)

## Examples

``` r
data(repllm_materials)

# Tier 1: local checks, no API key required
validate_auto(repllm_materials, condition_col = "frame")
#> 
#> ── Automatic validation ──
#> 
#> ✔ Length balance: max deviation 4.8%
#> ✔ Readability: grade-level spread 0.4
#> ! Manipulation leakage: 2/24 materials name a condition term.
#> ✔ Distinctiveness: Jaccard overlap in [0.1, 0.11]
#> ! Needs attention: "leakage"
#> Automatic validation
#>   Materials: 24 across 3 conditions
#>   length          PASS
#>   readability     PASS
#>   leakage         WARNING
#>   distinctiveness PASS
#>   Overall: needs attention 

# The two materials that name their own condition
leak <- check_manipulation_leakage(repllm_materials$text,
                                   repllm_materials$frame)
#> ! Manipulation leakage: 2/24 materials name a condition term.
leak$flagged[, c("condition", "term")]
#> # A tibble: 2 × 2
#>   condition  term      
#>   <chr>      <chr>     
#> 1 economic   economic  
#> 2 scientific scientific
```
