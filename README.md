# One-Step Limits of Randomized Speculative Decoding

The repository supports reproducibility for the empirical checks reported in the paper. These checks illustrate the one-step accepted-prefix abstraction studied in the manuscript. They are not end-to-end speculative-decoding serving benchmarks and do not measure wall-clock inference speedup.

## Paper summary

The paper studies a one-step abstraction of speculative decoding. For a fixed context and a parallel proposal budget P, a non-anticipating policy proposes up to P candidate continuations before observing verifier feedback. The accepted length is the longest prefix that agrees with the verifier continuation.

The main theorem shows that, for any verifier prefix law and any deterministic or randomized non-anticipating policy,

```math
\Pr(L_P \ge \ell) \le M_P(\ell)
```

where M_P(ℓ) is the largest verifier-prefix probability mass coverable by any P length-ℓ prefixes.

The result identifies covered verifier-prefix mass as the fixed-depth bottleneck in this one-step model. Randomization cannot improve on the best deterministic prefix cover at a fixed depth when the verifier prefix law is fixed.

## Scope of the experiments

The experiments in this repository are inference-only checks of the one-step accepted-prefix model. They are designed to illustrate the logarithmic-in-P behavior and diminishing returns predicted by the theory.

They do not model or measure:

- draft-model cost
- verifier batching
- KV-cache reuse
- correction mechanisms
- shared-prefix tree computation
- memory movement
- scheduling
- hardware latency
- wall-clock serving throughput

The logits experiments also do not perform full autoregressive rollout. They use next-token distributions obtained from model logits and then evaluate the stylized one-step accepted-prefix abstraction.

## Experimental components

The empirical checks consist of three parts.

### 1. Synthetic verifier laws

The synthetic experiments evaluate stylized i.i.d. verifier distributions over a fixed vocabulary. The distributions include:

- uniform laws
- Zipf laws
- Dirichlet-sampled laws

The purpose is to check whether the accepted-prefix length grows approximately linearly in log P, with steeper growth for more concentrated distributions.

The main settings used in the manuscript are:

- vocabulary size: V = 2000
- horizon: T = 64
- proposal budgets: P = 2^k, for k = 0,...,12
- Monte Carlo trials: 800 per condition

### 2. Logits-based checks

The logits experiments use next-token distributions obtained from causal language models. Logits are taken at the final prompt position, truncated to the top 2000 tokens, and renormalized.

The manuscript reports experiments using model identifiers including:

- `gpt2`
- `distilgpt2`
- `Qwen/Qwen2.5-0.5B`
- `Qwen/Qwen2.5-1.5B`
- `google/gemma-2-2b`
- `meta-llama/Llama-3.2-1B`
- `microsoft/phi-2`

No model training or fine-tuning is performed.

The GPT-2 logits experiment uses:

- horizon: T = 48
- proposal budgets: P = 2^k, for k = 0,...,11
- prompts: 100
- trials per prompt-budget pair: 600
- truncation: top 2000 tokens
- renormalization after truncation

### 3. Policy-comparison check

The policy-comparison experiment compares simple randomized candidate generation with deterministic top-mass coverage under a Zipf verifier law.

The comparison is intended to illustrate the covered-mass interpretation of the theorem: within the one-step model, accepted-prefix performance is governed by how much verifier-prefix mass the candidate set covers, not by randomization as such.

## Reproducibility information

The repository records the information needed to reproduce the empirical checks, including:

- prompt list
- random seeds
- model identifiers
- truncation rules
- proposal-budget grid
- horizon values
- Monte Carlo trial counts
- plotting scripts
- configuration files

For exact reproduction of the manuscript figures and tables, use the recorded configuration files and seeds.

## Installation

Use a fresh Python environment.

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

The experiments require standard scientific Python packages and, for the logits-based checks, PyTorch and Hugging Face Transformers.

## Running the experiments

Run the provided scripts from the repository root. The workflow is:

1. generate or load the synthetic verifier laws;
2. run the finite-P accepted-prefix simulations;
3. extract next-token logits for the selected language models;
4. apply top-2000 truncation and renormalization;
5. compute the candidate-integrated finite-P estimates;
6. generate the figures and tables reported in the manuscript.

## Output

The experiments produce:

- finite-T scaling summaries;
- synthetic-law scaling plots;
- GPT-2 logits scaling plots;
- multi-model logits scaling plots;
- policy-comparison tables.

These outputs correspond to the empirical section and appendix of the manuscript.

## Citation

If you use this repository, please cite the associated manuscript:

```bibtex
@article{javidnia2026onestep,
  author  = {Javidnia, Hossein},
  title   = {One-Step Limits of Randomized Speculative Decoding},
  journal = {TBC},
  year    = {2026},
  note    = {Manuscript under review}
}
```

## License

This repository is released under the Apache License 2.0. See `LICENSE` for details.
