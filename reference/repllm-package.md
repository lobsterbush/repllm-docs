# repllm: Generate and check experimental materials

I built this package to help answer a question: do model-written
experimental materials convey the manipulation I asked for? It generates
texts from a factorial design, checks their length and wording, and
helps collect ratings from models and people.

## Details

ellmer handles the model calls and credentials. The local checks run
without an API key. I'd compare model and human ratings of the same
materials before deciding how much to rely on the model ratings.

## Where this came from

The package grew out of Porter and Velez (2022). Their work motivates
using several stimuli per condition. I've added tools for checking what
those stimuli convey before fielding an experiment.

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

I'd run steps 4 and 5 on the same materials and compare the results.
Agreement in condition rankings can still come with different rating
gaps.

## Development record

Charles Crabtree maintains the package. OpenAI Codex assisted with the
September 2026 audit and revisions. The installed PROVENANCE file
describes this work and the separate records attached to generated
materials.

## References

Porter, E., & Velez, Y. R. (2022). Placebo selection in survey
experiments: An agnostic approach. *Political Analysis*, 30(4), 481-494.

## See also

Useful links:

- <https://lobsterbush.github.io/repllm-docs/>

- Report bugs at <https://github.com/lobsterbush/repllm-docs/issues>

## Author

**Maintainer**: Charles Crabtree <charles.crabtree@monash.edu>
([ORCID](https://orcid.org/0000-0001-5144-8671))
