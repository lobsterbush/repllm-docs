# Generated Experimental Materials

Twenty-four short vignettes about a carbon tax, written by an LLM from a
factorial design crossing three frames with two speaker types, four
realisations per cell. Together with
[repllm_synthetic](https://lobsterbush.github.io/repllm-docs/reference/repllm_synthetic.md)
and
[repllm_human](https://lobsterbush.github.io/repllm-docs/reference/repllm_human.md)
these form one complete simulated validation study, so every function in
the package can be demonstrated without an API key.

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

I built these to be instructive rather than flattering. They're balanced
on length and reading level, but two of them name their own condition,
so
[`check_manipulation_leakage`](https://lobsterbush.github.io/repllm-docs/reference/check_manipulation_leakage.md)
fires and
[`validate_auto`](https://lobsterbush.github.io/repllm-docs/reference/validate_auto.md)
comes back needing attention.

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
