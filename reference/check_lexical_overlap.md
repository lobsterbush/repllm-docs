# Check Lexical Distinctiveness Across Conditions

Compute pairwise Jaccard similarity between the vocabularies of each
condition. Very high overlap suggests the conditions aren't really
different. Very low overlap suggests they differ on more than the
dimension you meant, so you can't put any effect down to the
manipulation alone.

## Usage

``` r
check_lexical_overlap(texts, conditions, min_overlap = 0.1, max_overlap = 0.9)
```

## Arguments

- texts:

  Character vector of materials.

- conditions:

  Condition labels.

- min_overlap:

  Jaccard similarity below which a note is emitted (default: 0.10).
  Advisory only; it does not affect `pass`.

- max_overlap:

  Jaccard similarity above which the check fails (default: 0.90).

## Value

A list with `pairwise`, `min_observed`, `max_observed`, and `pass`.

## Examples

``` r
check_lexical_overlap(
  c("The tax creates jobs and growth.", "The tax protects our children.",
    "Jobs and growth follow the tax.", "Our children deserve protection."),
  c("economic", "moral", "economic", "moral")
)
#> ✔ Distinctiveness: Jaccard overlap in [0.17, 0.17]
#> $pairwise
#> # A tibble: 1 × 3
#>   condition_1 condition_2 jaccard
#>   <chr>       <chr>         <dbl>
#> 1 economic    moral         0.167
#> 
#> $min_observed
#> [1] 0.1666667
#> 
#> $max_observed
#> [1] 0.1666667
#> 
#> $pass
#> [1] TRUE
#> 
```
