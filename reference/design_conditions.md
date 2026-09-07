# Build a Factorial Design of Experimental Conditions

Cross a set of experimental factors into a design matrix, one row per
condition. This is where a study starts, and it's what
[`generate_materials`](https://lobsterbush.github.io/repllm-docs/reference/generate_materials.md)
takes as input: every column you name here becomes a `{placeholder}` you
can drop into the generation template. Use `.exclude` to drop cells that
make no sense, so you aren't asking a model to write a stimulus nobody
would ever field.

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
