# Randomise Execution Order

Shuffle the rows of a design. Randomising the generation order guards
against order effects in providers that keep any request-level state,
and you need to randomise presentation order before showing materials to
raters.

## Usage

``` r
randomize_design(design, seed = NULL)
```

## Arguments

- design:

  A data frame to shuffle.

- seed:

  Integer seed. Supply one for a reproducible shuffle.

## Value

The design with rows in random order.

## Examples

``` r
d <- design_conditions(frame = c("a", "b", "c"))
#> ℹ Design: 3 conditions from 1 factor
randomize_design(d, seed = 42)
#> # A tibble: 3 × 2
#>   condition_id frame
#>          <int> <chr>
#> 1            1 a    
#> 2            3 c    
#> 3            2 b    
```
