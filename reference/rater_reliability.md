# Check agreement among raters

Calculate inter-rater reliability for each dimension using the
intraclass correlation and ordinal Krippendorff's alpha. The function
also reports missing ratings and the number of complete materials used
for the ICC.

## Usage

``` r
rater_reliability(ratings)
```

## Arguments

- ratings:

  A long ratings tibble with a `rater` column.

## Value

A tibble with one row per dimension: `n_raters`, `n_materials`,
`n_complete` (materials every rater scored, which is what the ICC is
computed on), `n_failed`, `icc_single` (ICC(A,1), the reliability of one
rater), `icc_average` (ICC(A,k), the reliability of their mean), and
`krippendorff_alpha`.

Report `icc_single` when a single rating is the unit of analysis and
`icc_average` when you average raters first. They can differ a lot: .67
and .89 on the same data is typical.

## Details

I'd specify whether I'm reporting the reliability of one rater or the
mean of several. I'd also be careful interpreting high agreement among
repeated calls to the same model: the raters share a model, and
agreement among them doesn't establish agreement with people.

## Examples

``` r
r <- data.frame(
  material_id = rep(paste0("m", 1:4), times = 2),
  condition   = rep(c("a", "a", "b", "b"), times = 2),
  rater       = rep(c("synthetic_1", "synthetic_2"), each = 4),
  dimension   = "tone",
  rating      = c(5, 6, 2, 3, 5, 5, 2, 4)
)
rater_reliability(r)
#> # A tibble: 1 × 8
#>   dimension n_raters n_materials n_complete n_failed icc_single icc_average
#>   <chr>        <int>       <int>      <int>    <int>      <dbl>       <dbl>
#> 1 tone             2           4          4        0      0.903       0.949
#> # ℹ 1 more variable: krippendorff_alpha <dbl>
```
