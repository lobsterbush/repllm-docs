# Agreement Among Raters

Report inter-rater reliability across raters for each dimension, for
either synthetic or human ratings. You get the intraclass correlation
for continuous scales and Krippendorff's alpha as an ordinal check.
Failed ratings are reported rather than quietly dropped.

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

High agreement among synthetic raters is necessary but it isn't
sufficient. At a low temperature and without distinct personas, model
raters agree with each other because they're nearly the same rater,
which tells you nothing about how well the construct is measured.

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
