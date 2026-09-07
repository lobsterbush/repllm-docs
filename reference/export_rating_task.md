# Export a Blinded Rating Task for Human Coders

Write a rating sheet your coders can fill in, plus a separate key file
linking each row back to its condition. The sheet has no condition
labels, no generation prompts, and no design columns, and its rows are
shuffled, so a coder can't work out the manipulation from the file.

## Usage

``` r
export_rating_task(
  materials,
  path,
  dimensions,
  key_path = NULL,
  scale = c(1, 7),
  n_raters = 1L,
  condition_col = "condition",
  text_col = "text",
  seed = 42
)
```

## Arguments

- materials:

  A data frame of materials to be rated.

- path:

  Path for the rating sheet CSV.

- dimensions:

  Named character vector of dimensions, as passed to
  [`synthetic_ratings`](https://lobsterbush.github.io/repllm/reference/synthetic_ratings.md).
  One blank column per dimension is added.

- key_path:

  Path for the key CSV. Defaults to `path` with a `_key` suffix.

- scale:

  Numeric length-2 vector of rating endpoints (default `c(1, 7)`). This
  is reported in the console message rather than written into the sheet,
  so tell your coders the endpoints yourself.

- n_raters:

  Number of coders who will each complete a copy. When greater than one,
  a separate shuffled sheet is written per coder.

- condition_col:

  Column holding the condition (default `"condition"`). Written to the
  key, never to the sheet.

- text_col:

  Column holding the material text (default `"text"`).

- seed:

  Integer seed for the shuffle (default: 42).

## Value

Invisibly, a list with `sheets` (paths written) and `key_path`.

## Details

Keep the key away from your coders.
[`import_human_ratings`](https://lobsterbush.github.io/repllm/reference/import_human_ratings.md)
rejoins it when the ratings come back.

## Examples

``` r
m <- data.frame(
  condition = c("economic", "moral"),
  text = c("Jobs and growth follow.", "We owe our children better.")
)
tmp <- tempfile(fileext = ".csv")
export_rating_task(m, tmp, dimensions = c(economic = "economic appeal"))
#> ✔ Wrote 1 blinded rating sheet; key at /var/folders/hj/4jw7nfmx44q2c83zpn3h2n6m0000gq/T//RtmpwppG3K/file1175b370421b6_key.csv
#> ℹ Rate each dimension from 1 to 7. Do not share the key with coders.
```
