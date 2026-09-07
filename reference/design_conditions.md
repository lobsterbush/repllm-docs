# Build the experimental conditions

Start with the factors you want to vary. This function crosses their
levels into a design with one row per condition. Each column becomes a
`{placeholder}` in the prompt passed to
[`generate_materials`](https://lobsterbush.github.io/repllm-docs/reference/generate_materials.md).

## Usage

``` r
design_conditions(..., .exclude = NULL)
```

## Arguments

- ...:

  Named character or factor vectors, one per experimental factor. For
  example `frame = c("economic", "moral")`,
  `source = c("expert", "layperson")`.

- .exclude:

  Optional function taking the design tibble and returning a logical
  vector. Rows where it returns `TRUE` are dropped, which is useful for
  removing impossible or off-limits factor combinations.

## Value

A tibble with one row per condition, a `condition_id` column, and one
column per factor.

## Details

I'd use `.exclude` to remove combinations that wouldn't make sense in
the study before asking a model to write them.

## Examples

``` r
design_conditions(
  frame  = c("economic", "moral"),
  source = c("expert", "layperson")
)
#> ℹ Design: 4 conditions from 2 factors
#> # A tibble: 4 × 3
#>   condition_id frame    source   
#>          <int> <chr>    <chr>    
#> 1            1 economic expert   
#> 2            2 moral    expert   
#> 3            3 economic layperson
#> 4            4 moral    layperson

# Drop an implausible cell
design_conditions(
  frame  = c("economic", "moral"),
  source = c("expert", "layperson"),
  .exclude = function(d) d$frame == "moral" & d$source == "expert"
)
#> ℹ Design: 3 conditions from 2 factors
#> # A tibble: 3 × 3
#>   condition_id frame    source   
#>          <int> <chr>    <chr>    
#> 1            1 economic expert   
#> 2            2 economic layperson
#> 3            3 moral    layperson
```
