# Contributing to repllm

Thanks for taking a look. I’d be glad to hear what you’re trying to do
with the package and where it falls short.

## Reporting a bug

Please open an
[issue](https://github.com/lobsterbush/repllm-docs/issues) with the
smallest example that reproduces the problem. Include
[`sessionInfo()`](https://rdrr.io/r/utils/sessionInfo.html) and
`packageVersion("repllm")` so I can check the environment you’re using.

I’m especially interested in cases where a check passes but the
materials still have an obvious problem. A short example would help me
understand what I’ve missed.

## Suggesting a feature

Tell me what you want to do and how it fits your study. I’m keeping this
package focused on generating experimental materials and checking them
before fielding. [ellmer](https://ellmer.tidyverse.org/) handles
provider calls, credentials, batching, and costs.

## Working on the code

The source repository is currently private. Email me for access if you’d
like to contribute code, then fork it and branch from `main`.

Please follow the existing naming conventions: `snake_case` for exported
functions and `.snake_case` for internal helpers. Internal helpers use
`@keywords internal` and `@noRd`. Exported functions need `@param`,
`@return`, `@export`, and `@examples` documentation.

For a bug fix, add a test that fails before the change and passes after
it. Use the bundled data or mock the ellmer call with
`local_mocked_bindings()`. The tests don’t need API keys, and I’d like
to keep them that way. You’ll find examples in `tests/testthat/`.

Then run:

``` r
devtools::document()
devtools::test()
devtools::check()
```

Please read any errors, warnings, or notes and mention unresolved ones
in your pull request. Add a short entry to `NEWS.md` explaining what
changed for the user.

Edit the documentation in `R/` and regenerate it with
[`devtools::document()`](https://devtools.r-lib.org/reference/document.html).
`NAMESPACE` and the files in `man/` are generated, so edits there won’t
survive the next build.

## Working together

Please be kind and specific when giving feedback. If something’s
unclear, [send me a note](mailto:charles.crabtree@monash.edu).
