# Compare reading levels across conditions

Estimate Flesch-Kincaid reading levels and compare their condition
means. The function uses quanteda.textstats when it's installed and a
syllable heuristic otherwise. The output tells you which method it used.

## Usage

``` r
check_readability(texts, conditions, threshold = 2)
```

## Arguments

- texts:

  Character vector of materials.

- conditions:

  Condition labels.

- threshold:

  Maximum acceptable difference in mean grade level between any two
  conditions (default: 2.0).

## Value

A list with `per_text`, `by_condition`, `max_diff`, `unmeasurable`
(conditions with no computable grade level, which fail the check),
`method`, `n_missing`, `n_no_condition`, and `pass`.

## Details

This is an English-language measure. I'd use a suitable measure for the
language of the study before interpreting differences in other texts.

## Examples

``` r
check_readability(
  c("The policy creates jobs by investing in new infrastructure.",
    "We must act now to protect future generations from harm."),
  c("economic", "moral")
)
#> ! Readability: grade-level spread 4.2 (> 2)
#> $per_text
#> # A tibble: 2 × 3
#>   condition text                                                        fk_grade
#>   <chr>     <chr>                                                          <dbl>
#> 1 economic  The policy creates jobs by investing in new infrastructure.     10.2
#> 2 moral     We must act now to protect future generations from harm.         6  
#> 
#> $by_condition
#> # A tibble: 2 × 4
#>   condition     n mean_grade sd_grade
#>   <chr>     <int>      <dbl>    <dbl>
#> 1 economic      1       10.2       NA
#> 2 moral         1        6         NA
#> 
#> $max_diff
#> [1] 4.2
#> 
#> $unmeasurable
#> character(0)
#> 
#> $method
#> [1] "quanteda.textstats::textstat_readability (Flesch.Kincaid)"
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
