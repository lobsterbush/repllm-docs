# Package index

## Design and generation

Start with the factors you want to vary, then ask a model to write
several materials per condition.

- [`design_conditions()`](https://lobsterbush.github.io/repllm-docs/reference/design_conditions.md)
  : Build the experimental conditions
- [`replicate_design()`](https://lobsterbush.github.io/repllm-docs/reference/replicate_design.md)
  : Add versions of each condition
- [`randomize_design()`](https://lobsterbush.github.io/repllm-docs/reference/randomize_design.md)
  : Shuffle the order of the conditions
- [`generate_materials()`](https://lobsterbush.github.io/repllm-docs/reference/generate_materials.md)
  : Generate materials for each condition
- [`generation_sensitivity()`](https://lobsterbush.github.io/repllm-docs/reference/generation_sensitivity.md)
  : Generate materials with several models

## Tier 1, automatic validation

These checks run on your machine. I’d use them to find problems in the
text before collecting ratings.

- [`validate_auto()`](https://lobsterbush.github.io/repllm-docs/reference/validate_auto.md)
  : Run the local text checks
- [`check_length_balance()`](https://lobsterbush.github.io/repllm-docs/reference/check_length_balance.md)
  : Compare text length across conditions
- [`check_readability()`](https://lobsterbush.github.io/repllm-docs/reference/check_readability.md)
  : Compare reading levels across conditions
- [`check_manipulation_leakage()`](https://lobsterbush.github.io/repllm-docs/reference/check_manipulation_leakage.md)
  : Find words that could give away the condition
- [`check_lexical_overlap()`](https://lobsterbush.github.io/repllm-docs/reference/check_lexical_overlap.md)
  : Compare vocabulary across conditions

## Tier 2, synthetic validation

Ask a model to score the texts on the dimensions you care about, without
showing it the condition labels.

- [`synthetic_ratings()`](https://lobsterbush.github.io/repllm-docs/reference/synthetic_ratings.md)
  : Collect model ratings of the materials
- [`synthetic_check()`](https://lobsterbush.github.io/repllm-docs/reference/synthetic_check.md)
  : Compare conditions using model ratings
- [`rater_reliability()`](https://lobsterbush.github.io/repllm-docs/reference/rater_reliability.md)
  : Check agreement among raters

## Tier 3, human validation

Sample materials for people to rate, prepare blinded sheets, and compare
their ratings with the model’s.

- [`sample_for_human_validation()`](https://lobsterbush.github.io/repllm-docs/reference/sample_for_human_validation.md)
  : Sample materials for people to rate
- [`export_rating_task()`](https://lobsterbush.github.io/repllm-docs/reference/export_rating_task.md)
  : Prepare blinded rating sheets
- [`import_human_ratings()`](https://lobsterbush.github.io/repllm-docs/reference/import_human_ratings.md)
  : Read the completed rating sheets
- [`human_check()`](https://lobsterbush.github.io/repllm-docs/reference/human_check.md)
  : Compare conditions using human ratings
- [`human_reliability()`](https://lobsterbush.github.io/repllm-docs/reference/human_reliability.md)
  : Check agreement among human coders

## Example data

I’ve included a worked example with 24 texts and simulated ratings. You
can run the analysis without an API key.

- [`repllm_materials`](https://lobsterbush.github.io/repllm-docs/reference/repllm_materials.md)
  : Example experimental materials
- [`repllm_synthetic`](https://lobsterbush.github.io/repllm-docs/reference/repllm_synthetic.md)
  : Example model ratings
- [`repllm_human`](https://lobsterbush.github.io/repllm-docs/reference/repllm_human.md)
  : Example human ratings
