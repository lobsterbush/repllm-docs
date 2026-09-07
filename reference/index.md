# Package index

## Design and generation

Build a factorial design of conditions and have a model write the
stimuli, one shared system prompt across every condition.

- [`design_conditions()`](https://lobsterbush.github.io/repllm/reference/design_conditions.md)
  : Build a Factorial Design of Experimental Conditions
- [`replicate_design()`](https://lobsterbush.github.io/repllm/reference/replicate_design.md)
  : Add Replicates to a Design
- [`randomize_design()`](https://lobsterbush.github.io/repllm/reference/randomize_design.md)
  : Randomise Execution Order
- [`generate_materials()`](https://lobsterbush.github.io/repllm/reference/generate_materials.md)
  : Generate Experimental Materials from a Factorial Design
- [`generation_sensitivity()`](https://lobsterbush.github.io/repllm/reference/generation_sensitivity.md)
  : Generate the Same Design Under Several Models

## Tier 1, automatic validation

Local, deterministic checks that need no API key. They establish that
conditions are comparable on nuisance dimensions.

- [`validate_auto()`](https://lobsterbush.github.io/repllm/reference/validate_auto.md)
  : Automatic Validation of Experimental Materials
- [`check_length_balance()`](https://lobsterbush.github.io/repllm/reference/check_length_balance.md)
  : Check Length Balance Across Conditions
- [`check_readability()`](https://lobsterbush.github.io/repllm/reference/check_readability.md)
  : Check Readability Across Conditions
- [`check_manipulation_leakage()`](https://lobsterbush.github.io/repllm/reference/check_manipulation_leakage.md)
  : Check Whether Materials Name Their Own Manipulation
- [`check_lexical_overlap()`](https://lobsterbush.github.io/repllm/reference/check_lexical_overlap.md)
  : Check Lexical Distinctiveness Across Conditions

## Tier 2, synthetic validation

An LLM rates every material, blind to condition, on the construct the
manipulation targets.

- [`synthetic_ratings()`](https://lobsterbush.github.io/repllm/reference/synthetic_ratings.md)
  : Rate Materials with Synthetic (LLM) Raters
- [`synthetic_check()`](https://lobsterbush.github.io/repllm/reference/synthetic_check.md)
  : Test Whether the Manipulation Moved the Intended Construct
- [`rater_reliability()`](https://lobsterbush.github.io/repllm/reference/rater_reliability.md)
  : Agreement Among Raters

## Tier 3, human validation

Blinded rating sheets for human coders on a stratified subsample.

- [`sample_for_human_validation()`](https://lobsterbush.github.io/repllm/reference/sample_for_human_validation.md)
  : Sample Materials for Human Validation
- [`export_rating_task()`](https://lobsterbush.github.io/repllm/reference/export_rating_task.md)
  : Export a Blinded Rating Task for Human Coders
- [`import_human_ratings()`](https://lobsterbush.github.io/repllm/reference/import_human_ratings.md)
  : Read Completed Human Ratings
- [`human_check()`](https://lobsterbush.github.io/repllm/reference/human_check.md)
  : Test the Manipulation Against Human Ratings
- [`human_reliability()`](https://lobsterbush.github.io/repllm/reference/human_reliability.md)
  : Agreement Among Human Coders

## Example data

One complete simulated validation study, so every function can be run
without an API key.

- [`repllm_materials`](https://lobsterbush.github.io/repllm/reference/repllm_materials.md)
  : Generated Experimental Materials
- [`repllm_synthetic`](https://lobsterbush.github.io/repllm/reference/repllm_synthetic.md)
  : Synthetic Ratings of the Example Materials
- [`repllm_human`](https://lobsterbush.github.io/repllm/reference/repllm_human.md)
  : Human Ratings of the Example Materials
