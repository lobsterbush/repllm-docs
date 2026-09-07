# Example human ratings

Simulated ratings in the format returned by
[`import_human_ratings`](https://lobsterbush.github.io/repllm-docs/reference/import_human_ratings.md).
Two human coders score all 24 materials in
[repllm_materials](https://lobsterbush.github.io/repllm-docs/reference/repllm_materials.md)
on the same three dimensions as
[repllm_synthetic](https://lobsterbush.github.io/repllm-docs/reference/repllm_synthetic.md).

## Usage

``` r
repllm_human
```

## Format

A tibble with 144 rows and 5 columns:

- material_id:

  Identifier matching
  [repllm_materials](https://lobsterbush.github.io/repllm-docs/reference/repllm_materials.md).

- condition:

  The framing condition of that material.

- rater:

  `"human_1"` or `"human_2"`.

- dimension:

  Rated dimension.

- rating:

  Integer rating on a 1 to 7 scale.

## Details

I've given these ratings more noise and smaller gaps on two dimensions
so there's a difference to inspect. These are teaching data; they aren't
ratings collected from people.

## See also

[repllm_materials](https://lobsterbush.github.io/repllm-docs/reference/repllm_materials.md),
[repllm_synthetic](https://lobsterbush.github.io/repllm-docs/reference/repllm_synthetic.md)

## Examples

``` r
data(repllm_human)
data(repllm_synthetic)

human_check(repllm_human, target = c(economic = "economic"))
#> Human validation
#>   Materials: 24 | raters: 2 | dimensions: 3 
#>   Ratings: 144
#>   Standard errors: cluster-robust by material (24 clusters) 
#>   Manipulation recovery:
#>     ok    economic     on economic     margin +2.44 over scientific   (d = +2.82)
#>   Recovered 1/1 intended contrasts

# Set the two tiers side by side
targets <- c(economic = "economic", moral = "moral",
             scientific = "scientific")
synthetic_check(repllm_synthetic, target = targets)$recovery
#> # A tibble: 3 × 8
#>   condition  dimension  mean_target nearest    nearest_mean margin margin_d
#>   <chr>      <chr>            <dbl> <chr>             <dbl>  <dbl>    <dbl>
#> 1 economic   economic          6.67 scientific         2.96   3.71     6.23
#> 2 moral      moral             6.58 scientific         3.21   3.37     6.67
#> 3 scientific scientific        5.88 economic           3.12   2.75     3.23
#> # ℹ 1 more variable: is_highest <lgl>
```
