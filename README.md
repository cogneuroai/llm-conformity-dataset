# LLM Conformity Dataset

This repository contains prompts and model responses for the experiments in *Who Do LLMs Trust? Human Experts Matter More Than Other LLMs*.

The data cover three YES/NO benchmarks (BoolQ, StrategyQA, ETHICS) and two experimental settings that add short social prior statements to the prompt.

## Tasks

- **BoolQ**: yes/no reading comprehension (`google/boolq`)
- **StrategyQA**: implicit multi-step reasoning (`voidful/StrategyQA`)
- **ETHICS**: moral judgment (`hendrycks/ethics`, commonsense subset)

Each dataset uses the same fixed set of 300 items across all models and conditions.

## Models

Results are included for four instruction-tuned LLMs:

- `x-ai/grok-3-mini`
- `meta-llama/llama-3.3-70b-instruct`
- `google/gemini-2.5-flash-lite`
- `deepseek/deepseek-v3.1-terminus`

All runs use deterministic decoding (`temperature = 0`).

## Experiments

### Experiment 1: Unanimous priors

Folder: `Experiment1_Unanimous_Priors/`

Each item is evaluated with no prior (`k = 0`) and with a unanimous prior attributed to one of:

- `friends`
- `experts`
- `LLMs`

Group size `k` is in `{1, 3, 5, 9}`. The prior answer is randomized to be correct or incorrect (balanced across items), so the prior carries no net information on average.

### Experiment 2: Human vs LLM conflict

Folder: `Experiment2_Human_LLM_Conflict/`

Each item is evaluated with two disagreeing priors (`k = 2`): a human source (`friends` or `experts`) and an "other LLM" source. For each item there are paired conditions where either the human is correct or the LLM is correct.

## Directory structure

Data are organized by experiment, then by dataset and model:

```text
Experiment1_Unanimous_Priors/
  ds=<dataset>__model=<model>/
    manifest.json # records the run configuration (dataset, model, decoding params, seed, etc.).
    results.csv
    results_logprobs.csv  # only included for Llama 3.3 70B on BoolQ

Experiment2_Human_LLM_Conflict/
  ds=<dataset>__model=<model>/
    manifest.json #records the run configuration (dataset, model, decoding params, seed, etc.).
    results.csv
    results_logprobs.csv  # only included for Llama 3.3 70B on BoolQ
```

## Data files

### `results.csv`

One row per (item, condition). The file includes the full prompt shown to the model and the parsed YES/NO response.

Common fields:

- `id`, `question`, `prompt`, `gold`
- `model_answer_raw`, `model_answer`, `strict_format`, `is_correct`

Condition metadata:

- Experiment 1: `source`, `k`, `prior_choice`, `prior_is_correct`, `conformity`
- Experiment 2: `human_source`, `human_choice`, `llm_choice`, `human_is_correct`, `llm_is_correct`

Additional columns (`maj`, `min`, `margin`, `split`, `unanimous`) summarize the prior vote counts.

### `results_logprobs.csv`

For Llama 3.3 70B on BoolQ only, we also include token-level log-probabilities for the final `YES` and `NO` options:

- `logprob_yes`, `logprob_no`
- `internal_conf_yes`, `internal_conf_no` (normalized over `{YES, NO}`)

## Paper

Anooshka Bajaj and Zoran Tiganj. *Who Do LLMs Trust? Human Experts Matter More Than Other LLMs*.
