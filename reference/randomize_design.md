# Shuffle the order of the conditions

Return the design rows in a random order. I'd shuffle the order before
generating materials or presenting them to raters. Supply a seed if you
want to reproduce the shuffle.

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
