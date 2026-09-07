# Check Length Balance Across Conditions

Compare word and character counts across conditions. A condition that
runs longer gives participants more to react to, so the manipulation
gets confused with sheer exposure.

## Usage

``` r
check_length_balance(texts, conditions, threshold = 0.2)
```

## Arguments

- texts:

  Character vector of materials. `NA` entries are excluded from the
  statistics and reported separately.

- conditions:

  Condition labels, same length as `texts`.

- threshold:

  Proportional deviation from the grand mean that triggers a warning
  (default: 0.20).

## Value

A list with `by_condition`, `grand_mean_words`, `max_deviation`,
`n_missing`, `n_no_condition`, and `pass`.

## Examples

``` r
check_length_balance(
  c("Short text here.", "A somewhat longer text appears here now.", "Brief."),
  c("treatment", "treatment", "control")
)
#> ! Length balance: max deviation 72.7% (> 20%)
#> $by_condition
#> # A tibble: 2 × 6
#>   condition     n mean_words sd_words mean_chars deviation
#>   <chr>     <int>      <dbl>    <dbl>      <dbl>     <dbl>
#> 1 control       1          1    NA             6     0.727
#> 2 treatment     2          5     2.83         28     0.364
#> 
#> $grand_mean_words
#> [1] 3.666667
#> 
#> $max_deviation
#> [1] 0.7272727
#> 
#> $n_missing
#> [1] 0
#> 
#> $n_no_condition
#> [1] 0
#> 
#> $pass
#> [1] FALSE
#> 
```
