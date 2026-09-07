# Agreement Among Human Coders

Inter-rater reliability across human coders, computed identically to
[`rater_reliability`](https://lobsterbush.github.io/repllm-docs/reference/rater_reliability.md)
for synthetic raters so the two are on the same footing.

## Usage

``` r
human_reliability(ratings)
```

## Arguments

- ratings:

  A long human ratings tibble with a `rater` column.

## Value

A tibble with one row per dimension.

## Examples

``` r
r <- data.frame(
  material_id = rep(paste0("m", 1:4), times = 2),
  rater       = rep(c("human_1", "human_2"), each = 4),
  dimension   = "economic",
  rating      = c(6, 5, 2, 3, 6, 6, 3, 2)
)
human_reliability(r)
#> # A tibble: 1 × 8
#>   dimension n_raters n_materials n_complete n_failed icc_single icc_average
#>   <chr>        <int>       <int>      <int>    <int>      <dbl>       <dbl>
#> 1 economic         2           4          4        0      0.899       0.947
#> # ℹ 1 more variable: krippendorff_alpha <dbl>
```
