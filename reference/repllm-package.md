# repllm: Generation and Validation of LLM-Written Experimental Materials

Tools for social scientists who use a language model to write their
experimental stimuli. The package generates materials from a factorial
design and then validates them in three tiers. Automatic checks run
locally and cost nothing. Synthetic checks have a model rate the
materials. Human checks do the same with coders on a subsample. Running
the last two on the same materials tells you whether the model's ratings
resemble what people actually see.

## Details

ellmer does all the model work: providers, credentials, concurrency,
structured output, and cost accounting. This package never calls an API
itself.

## Where this came from

The package grew out of Porter and Velez (2022). Their argument is that
picking one placebo, or one stimulus, hands the researcher more freedom
than anyone should want, and that the fix is to generate a set and
average over it. That logic doesn't stop at generation. If a model wrote
the set, you didn't choose any of them and you can't vouch for what they
do to a respondent, so the generation has to arrive with measurement
attached. The three tiers here are that measurement.

## Workflow

1.  [`design_conditions`](https://lobsterbush.github.io/repllm-docs/reference/design_conditions.md)
    builds the factorial design.

2.  [`generate_materials`](https://lobsterbush.github.io/repllm-docs/reference/generate_materials.md)
    writes the stimuli.

3.  [`validate_auto`](https://lobsterbush.github.io/repllm-docs/reference/validate_auto.md)
    runs local confound checks.

4.  [`synthetic_ratings`](https://lobsterbush.github.io/repllm-docs/reference/synthetic_ratings.md)
    and
    [`synthetic_check`](https://lobsterbush.github.io/repllm-docs/reference/synthetic_check.md)
    test whether the manipulation moved the intended construct.

5.  [`sample_for_human_validation`](https://lobsterbush.github.io/repllm-docs/reference/sample_for_human_validation.md),
    [`export_rating_task`](https://lobsterbush.github.io/repllm-docs/reference/export_rating_task.md),
    [`import_human_ratings`](https://lobsterbush.github.io/repllm-docs/reference/import_human_ratings.md),
    and
    [`human_check`](https://lobsterbush.github.io/repllm-docs/reference/human_check.md)
    do the same with human coders.

Steps 4 and 5 answer the same question with different raters, so run
both on the same materials and set the results side by side.

## References

Porter, E., & Velez, Y. R. (2022). Placebo selection in survey
experiments: An agnostic approach. *Political Analysis*, 30(4), 481-494.

## See also

Useful links:

- <https://github.com/lobsterbush/repllm>

- Report bugs at <https://github.com/lobsterbush/repllm/issues>

## Author

**Maintainer**: Charles Crabtree <charles.crabtree@monash.edu>
([ORCID](https://orcid.org/0000-0001-5144-8671))
