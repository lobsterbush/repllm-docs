# Add Replicates to a Design

Expand each condition by `n` replicates. In stimulus generation this is
the multiple-realisations logic of Porter and Velez (2022). Generating
several independent versions per condition and averaging over them takes
away the freedom to pick the one stimulus you happen to like.

## Usage

``` r
replicate_design(design, n = 3)
```

## Arguments

- design:

  A tibble from
  [`design_conditions`](https://lobsterbush.github.io/repllm-docs/reference/design_conditions.md).

- n:

  Number of replicates per condition (default: 3).

## Value

The expanded design with a `replicate` column.

## References

Porter, E., & Velez, Y. R. (2022). Placebo selection in survey
experiments: An agnostic approach. *Political Analysis*, 30(4), 481-494.

## Examples

``` r
d <- design_conditions(frame = c("economic", "moral"))
#> ℹ Design: 2 conditions from 1 factor
replicate_design(d, n = 3)
#> # A tibble: 6 × 3
#>   condition_id frame    replicate
#>          <int> <chr>        <int>
#> 1            1 economic         1
#> 2            1 economic         2
#> 3            1 economic         3
#> 4            2 moral            1
#> 5            2 moral            2
#> 6            2 moral            3
```
