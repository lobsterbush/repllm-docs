# Find words that could give away the condition

Look for condition labels or other terms you've asked the function to
flag. A text that calls its own argument "economic" could prompt a rater
to score the label. I'd read the flagged texts to decide whether they
express the intended argument or simply name it.

## Usage

``` r
check_manipulation_leakage(texts, conditions, terms = NULL, threshold = 0)
```

## Arguments

- texts:

  Character vector of materials.

- conditions:

  Condition labels.

- terms:

  Character vector of terms that should not appear. Defaults to the
  unique condition labels. Terms shorter than three characters are
  skipped, with a message, because they match too much to be
  informative. If your arms are named something like `"T1"` and `"C"`,
  pass the words you actually care about here.

- threshold:

  Maximum acceptable proportion of materials containing a flagged term
  (default: 0, i.e. any leakage fails).

## Value

A list with `flagged` (tibble of offending materials), `by_condition`,
`leak_rate`, and `pass`.

## Examples

``` r
check_manipulation_leakage(
  c("This economic argument is about jobs.", "We owe our children a future."),
  c("economic", "moral")
)
#> ! Manipulation leakage: 1/2 materials name a condition term.
#> $flagged
#> # A tibble: 1 × 4
#>   index condition term     text                                 
#>   <int> <chr>     <chr>    <chr>                                
#> 1     1 economic  economic This economic argument is about jobs.
#> 
#> $by_condition
#> # A tibble: 2 × 4
#>   condition     n n_flagged leak_rate
#>   <chr>     <int>     <int>     <dbl>
#> 1 economic      1         1         1
#> 2 moral         1         0         0
#> 
#> $leak_rate
#> [1] 0.5
#> 
#> $pass
#> [1] FALSE
#> 
```
