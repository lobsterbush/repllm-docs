# Synthetic Ratings of the Example Materials

Simulated output of
[`synthetic_ratings`](https://lobsterbush.github.io/repllm-docs/reference/synthetic_ratings.md):
three LLM raters score all 24 materials in
[repllm_materials](https://lobsterbush.github.io/repllm-docs/reference/repllm_materials.md)
on three dimensions.

## Usage

``` r
repllm_synthetic
```

## Format

A tibble with 216 rows and 5 columns:

- material_id:

  Identifier matching
  [repllm_materials](https://lobsterbush.github.io/repllm-docs/reference/repllm_materials.md).

- condition:

  The framing condition of that material.

- rater:

  `"synthetic_1"` to `"synthetic_3"`.

- dimension:

  Rated dimension: `"economic"`, `"moral"`, or `"scientific"`.

- rating:

  Integer rating on a 1 to 7 scale.

## Details

The simulation has the synthetic raters recover every intended
manipulation while exaggerating two of them. On the economic and moral
dimensions the separation between conditions is amplified relative to
the human coders. On the scientific dimension it isn't. Run
[`synthetic_check`](https://lobsterbush.github.io/repllm-docs/reference/synthetic_check.md)
on these and
[`human_check`](https://lobsterbush.github.io/repllm-docs/reference/human_check.md)
on
[repllm_human](https://lobsterbush.github.io/repllm-docs/reference/repllm_human.md),
then set the two side by side.

## See also

[repllm_materials](https://lobsterbush.github.io/repllm-docs/reference/repllm_materials.md),
[repllm_human](https://lobsterbush.github.io/repllm-docs/reference/repllm_human.md)

## Examples

``` r
data(repllm_synthetic)

synthetic_check(
  repllm_synthetic,
  target = c(economic = "economic", moral = "moral",
             scientific = "scientific")
)
#> Synthetic validation
#>   Materials: 24 | raters: 3 | dimensions: 3 
#>   Ratings: 216
#>   Standard errors: cluster-robust by material (24 clusters) 
#>   Manipulation recovery:
#>     ok    economic     on economic     margin +3.71 over scientific   (d = +6.23)
#>     ok    moral        on moral        margin +3.37 over scientific   (d = +6.67)
#>     ok    scientific   on scientific   margin +2.75 over economic     (d = +3.23)
#>   Recovered 3/3 intended contrasts

rater_reliability(repllm_synthetic)
#> # A tibble: 3 × 8
#>   dimension  n_raters n_materials n_complete n_failed icc_single icc_average
#>   <chr>         <int>       <int>      <int>    <int>      <dbl>       <dbl>
#> 1 economic          3          24         24        0      0.935       0.977
#> 2 moral             3          24         24        0      0.930       0.975
#> 3 scientific        3          24         24        0      0.716       0.883
#> # ℹ 1 more variable: krippendorff_alpha <dbl>
```
