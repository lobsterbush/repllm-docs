# Compare vocabulary across conditions

Calculate pairwise Jaccard similarity between the vocabularies of each
condition. High overlap can help identify conditions with very similar
wording. Low overlap is a reason to read the texts and ask what else may
have changed.

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

## Details

I'd treat this as a diagnostic. Vocabulary overlap also depends on text
length and how many materials are in each condition. Low overlap is
advisory; it doesn't fail the check.

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
