# Read Completed Human Ratings

Read one or more completed rating sheets, rejoin the condition key, and
reshape to the long format the rest of the package uses, so you can
analyse human ratings with the same functions as synthetic ones.

## Usage

``` r
import_human_ratings(path, key_path, dimensions = NULL, scale = NULL)
```

## Arguments

- path:

  Path to a completed sheet, or a character vector of paths.

- key_path:

  Path to the key written by
  [`export_rating_task`](https://lobsterbush.github.io/repllm-docs/reference/export_rating_task.md).

- dimensions:

  Character vector of dimension column names to read. If `NULL`, every
  column other than `material_id`, `text`, and `rater` is treated as a
  dimension.

- scale:

  Optional numeric length-2 vector. Ratings outside the scale are set to
  `NA` and reported.

## Value

A long tibble with `material_id`, `condition`, `rater`, `dimension`, and
`rating`.

## Examples

``` r
if (FALSE) { # \dontrun{
human <- import_human_ratings(
  c("ratings_rater1.csv", "ratings_rater2.csv"),
  key_path = "ratings_key.csv"
)
human_check(human, target = c(economic = "economic", moral = "moral"))
} # }
```
