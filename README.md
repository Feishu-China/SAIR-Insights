# SAIR-Insights

> Independent community-driven analysis of the [SAIR Math Distillation Challenge](https://competition.sair.foundation) — distilling mathematical reasoning into compact LLM prompts.

## Overview

The **SAIR Math Distillation Challenge** (Feb–Apr 2026), initiated by Fields Medalist Terence Tao, asked a deceptively simple question: *Can we distill deep mathematical reasoning about equational theories into a ≤10KB text prompt (a "cheatsheet") that helps LLMs solve Magma implication problems?*

Over 50 researchers worldwide participated, generating 414+ discussion messages on the SAIR Zulip channel. This repository presents an independent, in-depth analysis of the community's discussions, technical discoveries, and their broader implications for AI research and industry.

## Live Preview

🌐 **[Interactive HTML Report (Bilingual)](https://feishu-research.github.io/SAIR-Insights/)** — Switch between Chinese and English with one click.

## Reports

📄 **Chinese Version**: [SAIR_Math_Distillation_Challenge_深度研究报告.md](./SAIR_Math_Distillation_Challenge_深度研究报告.md)

📄 **English Version**: [SAIR_Math_Distillation_Challenge_Deep_Research_Report.md](./SAIR_Math_Distillation_Challenge_Deep_Research_Report.md)

📄 **Interactive HTML**: [index.html](./index.html) (bilingual, self-contained preview)

### Report Structure (9 chapters + appendices)

| Ch. | Topic |
|-----|-------|
| I | Competition Background & Core Problem |
| II | Timeline & Key Events |
| III | Core Technical Discoveries (incl. AlphaEvolve, Het S Visualization, Community Infrastructure) |
| IV | Playground & Evaluation Infrastructure |
| V | Participant Ecosystem & Collaboration Patterns |
| VI | Key Controversies & Open Questions |
| VII | Implications for AI Mathematical Reasoning |
| VIII | Special Topic: Cázares' Paper — "Less Is More: Single-Prompt Ceiling" |
| IX | Stage 2 Outlook |

## Key Findings at a Glance

- **Small model + good prompt > large model**: Llama-3.1-8B (8B params) with a well-designed cheatsheet reached 77% on hard sets — outperforming bare trillion-parameter models
- **Single-Prompt Ceiling**: Accuracy improvements become unstable and non-generalizable beyond ~71–79% (Cázares, 2026)
- **Merge pattern is avg(A,B), not max(A,B)**: Combining complementary prompts yields arithmetic mean performance, not the best of both
- **Router Hypothesis**: LLMs behave as heuristic classifiers over algebraic structure patterns, not symbolic theorem provers
- **Cross-distribution fragility**: Only 1 of 52 public submissions exceeded 65% on both hard2 and hard3 simultaneously

## Data Source

- **Zulip Channel**: [Math Distillation Challenge - equational theories](https://zulip.sair.foundation) (414 messages, 302KB)
- **Raw data**: [math_distillation_discussions.csv](./data/math_distillation_discussions.csv) — exported via Zulip API
- **Paper analyzed**: Cázares, M. I. (2026). *Less Is More: Cognitive Load and the Single-Prompt Ceiling in LLM Mathematical Reasoning*

## Related Resources

| Resource | Link |
|----------|------|
| SAIR Playground | https://playground.sair.foundation |
| Official Benchmark | https://benchmark.sair.foundation |
| Contributor Network | https://competition.sair.foundation/contributor-network |
| Official Eval Code | https://github.com/SAIRcompetition/equational-theories-stage1-judge |
| ETP Project (Tao) | https://teorth.github.io/equational_theories/ |
| Cázares' Paper Code | https://github.com/israelcazares/sair-prompt-engineering |

## About

This is an **independent analysis** by Feishu Research Institute. It is not affiliated with, endorsed by, or connected to SAIR (Safe AI Research) Foundation or any competition organizers. All analysis represents our own interpretation of publicly available community discussions.

## License

This repository is released under [CC BY-NC-SA 4.0](./LICENSE).
