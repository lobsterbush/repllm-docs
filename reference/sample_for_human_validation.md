# Sample Materials for Human Validation

Draw a subsample of materials for human rating, stratified by condition
so every cell shows up. Human coding is the expensive tier, so it
usually runs on a subsample. Run
[`human_check`](https://lobsterbush.github.io/repllm-docs/reference/human_check.md)
on it and set the result beside
[`synthetic_check`](https://lobsterbush.github.io/repllm-docs/reference/synthetic_check.md)
to see whether the two tiers agree.

## Usage

``` r
sample_for_human_validation(
  materials,
  n,
  stratify_by = "condition",
  min_per_stratum = 1L,
  text_col = "text",
  seed = 42
)
```

## Arguments

- materials:

  A data frame of materials.

- n:

  Total number of materials to sample.

- stratify_by:

  Column name to stratify on (default: `"condition"`). Use `NULL` for a
  simple random sample.

- min_per_stratum:

  Minimum number of materials drawn from each stratum before
  proportional allocation (default: 1). This guarantees that every
  condition is represented even when one is rare. Set to 0 for purely
  proportional allocation.

- text_col:

  Name of the text column (default: `"text"`).

- seed:

  Integer seed (default: 42).

## Value

A tibble: the sampled rows of `materials`, in their original column
order, plus `material_id` if it was absent.

## Details

Allocation gives every stratum `min_per_stratum` materials first, then
distributes what is left by largest remainder, never exceeding a
stratum's size. A stratum with a single member is drawn correctly.
Materials with a missing stratum value are excluded and reported, so you
get `min(n, <materials with a stratum value>)` rows rather than
`min(n, nrow(materials))` whenever any label is missing.

## Examples

``` r
m <- data.frame(
  condition = rep(c("economic", "moral"), each = 10),
  text = paste("vignette", 1:20)
)
sample_for_human_validation(m, n = 6, seed = 42)
#> ✔ Sampled 6 materials from 2 of 2 stratum/strata
#> # A tibble: 6 × 3
#>   condition text        material_id
#>   <chr>     <chr>       <chr>      
#> 1 economic  vignette 1  m0001      
#> 2 economic  vignette 5  m0005      
#> 3 economic  vignette 10 m0010      
#> 4 moral     vignette 12 m0012      
#> 5 moral     vignette 14 m0014      
#> 6 moral     vignette 19 m0019      
```
