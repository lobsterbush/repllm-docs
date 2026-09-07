# Compare conditions using human ratings

Analyse human ratings with the same estimator used by
[`synthetic_check`](https://lobsterbush.github.io/repllm-docs/reference/synthetic_check.md).
The result includes condition means and contrasts with robust confidence
intervals. Standard errors cluster by material when several coders rate
the same text.

## Usage

``` r
human_check(ratings, target = NULL, conf_level = 0.95)
```

## Arguments

- ratings:

  A long tibble from
  [`import_human_ratings`](https://lobsterbush.github.io/repllm-docs/reference/import_human_ratings.md).

- target:

  Optional named character vector mapping condition levels to the
  dimension each is meant to maximise.

- conf_level:

  Confidence level for intervals (default: 0.95).

## Value

An object of class `rating_validation`.

## Details

I'd compare the human and model results on the same materials. Similar
condition rankings can still come with different rating gaps.

## Examples

``` r
ratings <- data.frame(
  material_id = rep(paste0("m", 1:4), each = 2),
  condition   = rep(c("economic", "economic", "moral", "moral"), each = 2),
  dimension   = rep(c("economic", "moral"), times = 4),
  rating      = c(6, 3, 6, 2, 3, 6, 2, 7)
)
human_check(ratings, target = c(economic = "economic", moral = "moral"))
#> Human validation
#>   Materials: 4 | raters: 1 | dimensions: 2 
#>   Ratings: 8
#>   Standard errors: HC2 
#>   Manipulation recovery:
#>     ok    economic     on economic     margin +3.50 over moral        (d = +7.00)
#>     ok    moral        on moral        margin +4.00 over economic     (d = +5.66)
#>   Recovered 2/2 intended contrasts
```
