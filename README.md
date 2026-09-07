# repllm documentation

Rendered documentation for [`repllm`](https://lobsterbush.github.io/repllm-docs/),
an R package for generating experimental stimuli with a language model and
validating them before they go into the field.

The site is at <https://lobsterbush.github.io/repllm-docs/>.

## What this repository is

Generated output only. Every file here is built by
[pkgdown](https://pkgdown.r-lib.org/) from the package source, which lives in a
separate private repository. Nothing here is edited by hand, so a pull request
against it would be overwritten on the next build.

If you spot something wrong in the documentation, please
[email me](mailto:charles.crabtree@monash.edu) rather than opening a pull
request, and I will fix it at the source.

## About the package

`repllm` generates stimuli from a factorial design and then validates them in
three tiers: local checks that need no API key, a model rating every material
blind to condition, and human coders on a subsample. All model communication is
delegated to [ellmer](https://ellmer.tidyverse.org/).

It grew out of Porter and Velez (2022), whose argument for generating a set of
stimuli rather than picking one is where the package starts.

> Porter, E., & Velez, Y. R. (2022). Placebo selection in survey experiments: An
> agnostic approach. *Political Analysis*, 30(4), 481-494.

## Author

Charles Crabtree, Senior Lecturer, School of Social Sciences, Monash University
and K-Club Professor, University College, Korea University.

MIT licensed, same as the package.
