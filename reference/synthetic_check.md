# Test Whether the Manipulation Moved the Intended Construct

Take a set of ratings, synthetic or human, and report condition means
with robust confidence intervals for every dimension. Give it `target`
and it also reports whether each condition scored highest on the
dimension it was meant to move, and how big that gap is in pooled
standard deviations.

## Usage

``` r
synthetic_check(ratings, target = NULL, conf_level = 0.95)
```

## Arguments

- ratings:

  A long tibble with `material_id`, `condition`, `dimension`, and
  `rating`, from
  [`synthetic_ratings`](https://lobsterbush.github.io/repllm-docs/reference/synthetic_ratings.md)
  or
  [`import_human_ratings`](https://lobsterbush.github.io/repllm-docs/reference/import_human_ratings.md).

- target:

  Optional named character vector mapping condition levels to the
  dimension each is meant to maximise, e.g.
  `c(economic = "economic", moral = "moral")`.

- conf_level:

  Confidence level for intervals (default: 0.95).

## Value

An object of class `rating_validation`.

## Details

Standard errors cluster by material when several raters rate the same
material, since those ratings aren't independent observations.

## See also

[`human_check`](https://lobsterbush.github.io/repllm-docs/reference/human_check.md),
[`rater_reliability`](https://lobsterbush.github.io/repllm-docs/reference/rater_reliability.md)

## Examples

``` r
ratings <- data.frame(
  material_id = rep(paste0("m", 1:4), each = 2),
  condition   = rep(c("economic", "economic", "moral", "moral"), each = 2),
  dimension   = rep(c("economic", "moral"), times = 4),
  rating      = c(6, 2, 7, 3, 2, 6, 3, 7)
)
synthetic_check(ratings, target = c(economic = "economic", moral = "moral"))
#> Synthetic validation
#>   Materials: 4 | raters: 1 | dimensions: 2 
#>   Ratings: 8
#>   Standard errors: HC2 
#>   Manipulation recovery:
#>     ok    economic     on economic     margin +4.00 over moral        (d = +5.66)
#>     ok    moral        on moral        margin +4.00 over economic     (d = +5.66)
#>   Recovered 2/2 intended contrasts
```
