# Sample materials for people to rate

Draw a sample of materials, usually stratified by condition. I'd use
this when rating the whole pool by hand would be too expensive. Analyse
the returned ratings with
[`human_check`](https://lobsterbush.github.io/repllm-docs/reference/human_check.md)
and compare them with
[`synthetic_check`](https://lobsterbush.github.io/repllm-docs/reference/synthetic_check.md)
on the same materials.

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
  proportional allocation (default: 1), when the requested sample is
  large enough. Omitted conditions are reported. Set to 0 for purely
  proportional allocation.

- text_col:

  Name of the text column (default: `"text"`).

- seed:

  Integer seed (default: 42).

## Value

A tibble: the sampled rows of `materials`, in their original column
order, plus `material_id` if it was absent.

## Details

When the sample is large enough, each stratum receives `min_per_stratum`
materials first. The remainder is allocated proportionally using largest
remainders, without exceeding the available materials. The function
reports strata that couldn't be included.

Materials with missing stratum labels are excluded and counted. The
number returned is therefore capped at the number with a stratum label.

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
