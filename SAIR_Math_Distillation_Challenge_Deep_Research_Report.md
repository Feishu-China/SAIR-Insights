# SAIR Math Distillation Challenge: The Magma Equational Implication Problem — In-Depth Research Report

> **Data Source**: All 414 discussion messages from the Zulip channel "Math Distillation Challenge - equational theories"
> **Time Span**: 2026-02-23 ~ 2026-04-27
> **Active Participants**: ~50+
> **Key Figures**: Terence Tao, YZ (SAIR Organizer), Adam Betka (stokarz), Heath Sanchez, Stacey Szmy, Gan W., et al.

---

## I. Competition Background and Core Problem

### 1.1 What is the Magma Equational Implication Problem?

This competition stems from the **Equational Theories Project (ETP)**, a large-scale formal mathematics project led by Terence Tao and others. The project investigates **equational implication relationships** for binary operations (called Magma):

> Given two equations $Eq_1$ and $Eq_2$, does $Eq_1$ imply $Eq_2$? That is, does it hold for **all** Magmas?

**Mathematical Background**:
- A Magma is the simplest algebraic structure—a single binary operation $\star$ with no axioms required (associativity, commutativity, etc.)
- Under the constraint of at most 4 operations, there are **4,694 distinct equations**
- These equations form approximately **22 million implication pairs**, all of which the ETP project has completely and individually resolved
- All solutions have been machine-verified using Lean 4 formal proof language

**Example Problem**:
```
Eq1: x * (y * x) = (x * z) * x
Eq2: x = x * (y * (x * (y * z)))
Answer: FALSE (a counterexample Magma exists)
```

### 1.2 Core Tasks of the Competition

**Stage 1 (March–April 2026)**:

| Dimension | Specification |
|:----------|:--------------|
| Submission | A **complete Prompt** (template + Cheatsheet), maximum **10 KB** |
| Evaluation Models | GPT-OSS-120B, Llama-3.3-70B, Gemma-4-31B-IT (equal-weight average) |
| Evaluation Set | **600 problems** (200 Normal + 200 Hard + 200 Extra Hard), completely disjoint from public sets |
| Evaluation Method | Each model × each problem run 3 times, total 5,400 inferences |
| Output Token Limit | 8,192 (main leaderboard) and 16,384 (secondary leaderboard) |
| Final Evaluation Temperature | temperature = 0.0 |
| Prompt Delivery | Passed as a single user prompt |

**Stage 2 (Starting May 2026)**:
- LLMs must not only determine TRUE/FALSE but also generate **Lean 4 formal proofs**
- Tool use (code execution, etc.) is planned to be allowed
- Open to all registered participants
- Cheatsheet size limits will be larger

### 1.3 The Meaning of "Distillation"

The core research question of the competition is:

> **Can deep mathematical reasoning ability be "distilled" into a compact, human-readable Cheatsheet (≤10 KB), thereby significantly improving weaker LLMs' performance on formal mathematics tasks?**

This is not fine-tuning, not training—purely **Prompt Engineering** for knowledge transfer.

---

## II. Competition Timeline and Key Events

### Phase One: Launch and Baseline Establishment (February–Mid March)

| Date | Event |
|:------|:------|
| Feb 23 | Channel created |
| Mar 8 | Channel renamed to current name |
| Mar 14 | Terence Tao publishes baseline results: SOTA models achieve only ~47% on Hard set |
| Mar 14 | **Dataset duplication issue exposed**—200 problems in Hard set contain only 69 unique pairs |
| Mar 15 | Terence Tao confirms random number generator encoding error; releases deduplicated hard1 (69 problems) and brand-new hard2 (200 problems) |
| Mar 15 | **Adam Betka first reaches 99.5%** (199/200 on hard1) |

### Phase Two: Technical Deepening and Rule Adjustments (Mid–Late March)

| Date | Event |
|:------|:------|
| Mar 15 | **Major Prompt ordering discussion**—community discovers placing Cheatsheet before task description significantly improves results |
| Mar 16 | SAIR updates Playground credit system: 1 credit ≈ $0.01, 10 credits daily |
| Mar 17 | Terence Tao shares list of hardest ETP implication problems (from Lean formalization project) |
| Mar 17 | **Adam Betka publishes breakthrough results**: GPT-OSS-120B reaches 99%, Llama-3.1-8B reaches 77% |
| Mar 17 | YZ announces upcoming Cheatsheet sharing and citation mechanism |
| Mar 19 | SAIR officially confirms: participants submit **complete Prompts** (not Cheatsheet alone) |
| Mar 22 | **Adam Betka releases SAIRCommunityBench v1** (200 problems), GPT-OSS-120B achieves only 78.5% |
| Mar 24 | hard3 dataset released |
| Mar 28 | **Model hallucination issue** discussed—GPT constructs erroneous proofs after finding counterexamples |

### Phase Three: Sprint and Submission (April)

| Date | Event |
|:------|:------|
| Apr 1 | **SAIR Contributor Network launched**—Cheatsheet sharing and automated baseline testing |
| Apr 2 | **Adam McKenna discovers the "Spine Isolation Theorem"** |
| Apr 9 | **Official Stage 1 evaluation code and model configurations released** |
| Apr 10 | Gemma-4 reasoning mode causing silent failure issue exposed |
| Apr 13 | Arjun Garg publishes rule enforcement analysis—step-by-step algorithms increase rule hit rate from 16.7% to 81% |
| Apr 15 | YZ confirms final evaluation set excludes 5th-order equations; separate 5th-order leaderboard established |
| Apr 18 | YZ publishes evaluation details: 600 problems, 3 models, 3 runs per pair |
| Apr 20 | **Stage 1 submission deadline** |
| Apr 22 | **Gan W. publishes high-generalization Cheatsheet**—three models perform nearly identically |
| Apr 24 | **Stage 2 pre-registration opens** |
| Apr 25 | YZ hints Stage 2 may allow participants to choose their own models (DeepSeek-V4, Kimi, etc.) |

---

## III. Core Technical Findings and Insights

### 3.1 Cheatsheet Methodology

Two major schools of thought emerged in the competition:

#### School One: Algebraic Proof Methods

**Key Figure**: Adam Betka (stokarz)

The core idea is to transform mathematical theorems into algebraic rules executable by LLMs:

- **Source-family Collapse Lemmas**: Classify equations into families; checking implication relationships requires only examining inter-family relationships
- **Ordered Invariant Decision Rules**: Decisions based on structural features such as spine depth and variable occurrence patterns
- **Symbolic Universal Solver**: Best-performing method for GPT-OSS models

Key Results:
- GPT-OSS-120B: 99% on hard1, 99% on hard2 (prompt tuning required for different datasets)
- GPT-OSS-20B: 98% on hard2
- Llama-3.3-70B: 88.5% on hard2
- **Llama-3.1-8B**: 77% on hard2 (most surprising result)

**Limitations**: Algebraic Cheatsheets show **poor generalization across datasets**—a prompt that saturates one dataset may drop significantly on another. Betka himself acknowledged this.

#### School Two: Structural Routing Methods

**Key Figures**: Heath Sanchez, Adam McKenna

Core idea: Rather than mathematical proofs, mine **structured discriminative rules** from the Implication Graph:

- **Spine Classification**: Classify equations by their syntactic tree structure (left spine, right spine, mixed spine)
- **"Checker vs. Mathematician" Paradigm**: Let LLMs act as programmatic checkers rather than free-reasoning agents
- **FALSE-first Strategy**: Mine stable FALSE rules; use TRUE rules conservatively

Heath Sanchez's key finding:
> "Current methods appear to memorize 'what it looks like,' which fails under distribution shift. Instead, distilling the true generative principles reveals 'why it must be so.' Once these principles are mastered, the same representation elegantly generalizes to unknown problems."

#### School Three: ZPYPIPE System

**Key Figure**: Stacey Szmy (Zer00logy)

A highly engineered custom DSL (Domain-Specific Language)-style Cheatsheet, using pseudocode to define a strict reasoning pipeline:

```
M.0.1 — Identify last 3 characters of Eq1
N2.9 / N3.2 / N3.4 — Confirm
L0.4 Execute: FORCE Eq1 = STATIC-LATTICE
         DISABLE ALL VV_* LAWS
         VERDICT: FALSE
```

This method's distinctive features:
- Forces LLMs to execute fixed procedures, greatly reducing hallucinations from "free reasoning"
- Verified to work on Claude, DeepSeek, GPT, Llama, and Grok
- Llama-3.3-70B achieved 100% on hard2 (single test)
- Described by Stacey as "code that slides into the AI priority layer system"

### 3.2 Key Findings in Prompt Engineering

#### Finding One: Prompt Ordering is Critical

One of the most important early discoveries in the community. Rai was the first participant to raise this issue, noting that in the original prompt template, dynamic equations appeared before the Cheatsheet, which would prevent utilization of the API caching mechanism.

**Rai's original comment (March 14)**:
> "The dynamic equations appear in block 2, before the potentially large cheatsheet. This means every request diverges near the top of the prompt, which prevents us from taking advantage of input caching."

Rai conducted rigorous ablation experiments on Gemini Flash Lite and GPT-OSS-120B (200 tasks × 3 Cheatsheets), showing reordering did not affect quality in most cases while significantly reducing output token consumption.

Adam Betka then confirmed this with large-scale testing (7.5M tokens):

| Order | Structure | Effect |
|:------|:----------|:-------|
| **Original order** | System → Task → Cheatsheet → Output format | Worse |
| **Optimized order** | System → Cheatsheet → Output format → Task | Better |

**Adam Betka's detailed observations** (March 15):
> "Confirming this, prompt structure should be reversed, with the cheatsheet being first. Not only to save the inference thanks to caching, but also due to non-reversed version producing worse results. (it also naturally makes sense for me from the mechanistic interp POV that it's better for the model to first receive the cheatsheet, and only then reason about the equations)."

> "What I saw was that on the run from around 85% to 95% reordering produced better results, and prompts that would score 95% on reordered version would still be at around 90% on a normal one. But for the final 99.5% one, the normal ordering was crucial (and it scored at around 98% on reordered version)."

**Reasons**:
1. Placing Cheatsheet first enables API caching (static portions shared across requests)
2. Having the model "learn" rules before "solving problems" aligns with cognitive patterns
3. From mechanistic interpretability perspective: having Cheatsheet activate relevant knowledge in the model's latent space first

#### Finding Two: VERDICT Position Affects Model Judgment

Rhys Edwards's profound insight:
> "Requiring the model to output VERDICT (TRUE/FALSE) first, then write the reasoning, essentially forces the model to answer immediately without any deliberation. This isn't 'guess first, think later'—it's 'guess and call it done'."

**Recommendation**: Place VERDICT at the end, letting the model complete reasoning before giving conclusions.

However, community discussion also noted that current mainstream models' Chain-of-Thought mechanisms already perform implicit reasoning internally, so VERDICT position has less impact than expected. SAIR ultimately allowed participants full customization of output format.

#### Finding Three: Different Models Require Different Prompt Styles

| Model | Best Prompt Style | Characteristics |
|:------|:------------------|:----------------|
| GPT-OSS series | Mathematical, natural-language algebraic rules | Strong comprehension, but unsuitable for complex flow control |
| Llama series | Strict step-by-step algorithms + worked examples | Needs explicit instructions, prone to "going off-track" |
| Gemma series | High freedom, various styles work | Consistent performance |
| Grok series | Similar to GPT, but more sensitive | Requires fine-tuning |

**Adam McKenna's experience**:
> "We optimized almost exclusively for Llama because it goes off-track so easily. Using a more theoretical sheet on GPT can achieve 80%+, but the same sheet crashes on Llama. Eventually we had to use a format all models could accept."

### 3.3 "Hallucination" and Self-Override Problems

A key issue discovered by Adam McKenna's team:

**Phenomenon**: After finding valid counterexamples in the Magma table, the model still "talks back" to construct a fabricated proof to override its own findings.

**Specific Case**:
```
Step 1: Model correctly computes E1 holds in test Magma ✓
Step 2: Model correctly computes E2 does NOT hold in test Magma ✓
Step 3: Model should conclude VERDICT: FALSE
Step 4: But model constructs a plausible "proof" claiming E1⇒E2 → VERDICT: TRUE ✗
```

**Root Cause**: The model possesses problem-solving capability but lacks metacognition to "trust its own computational results."

**Solutions**:
- Add guardrail language in Cheatsheet: "Counterexamples are mathematical proofs and cannot be overturned"
- Adopt "Checker Mode" rather than "Mathematician Mode"—stop immediately upon finding counterexamples
- Use pseudocode style to constrain model freedom

### 3.4 Generalization vs. Overfitting

The central recurring debate in the competition:

**Overfitting Phenomenon**:
- Adam Betka's GPT Cheatsheet can achieve 99% on hard1 and 99% on hard2—but these are two different prompts
- Cheatsheets tuned for dataset distribution show significant drops on new distributions
- Josu San Martin published 100% on hard3, but this dropped to ~82% under expanded testing

**Generalization Signals**:
- Gan W.'s published Cheatsheet EQT01-000145 shows remarkably consistent performance across all public datasets (57%–80%), with minimal inter-model variance
- Adam Betka's **algebraic Cheatsheets** show significantly better cross-dataset generalization than symbolic solver Cheatsheets

**Terence Tao's perspective**:
> "From a mathematical standpoint, discovering more general and accurate heuristics that apply to higher-order equations would certainly be more meaningful... But initially I thought describing just the initial 22 million implication set would be challenging enough."

**Heath Sanchez's summary**:
> "Stage 1 is less about finding a magical proof prompt and more about: mining robust structural FALSE rules, plus a very small number of conservative TRUE triggers, then faithfully translating them into strict programmatic checker prompts."

### 3.5 Mathematical Contributions

Several results with independent mathematical value emerged during the competition:

#### Spine Isolation Theorem

**Discoverers**: Adam McKenna's team

**Content**: Classifying equations by spine (path from root to x), proving:
- Pure left-spine equations (depth n) cannot imply: right-spine equations, mixed-spine equations, or left-spine equations of another depth m (when n ∤ m)
- Using cyclic groups ($\mathbb{Z}/n\mathbb{Z}$, $a \star b = a+1 \mod n$) as separating Magmas
- Verified on the complete 4,694×4,694 implication graph: **zero exceptions among 1.54 million pairs**

**Formalization**: Lean 4 proof open-sourced
- Preprint: https://zenodo.org/records/19380600
- Code: https://github.com/mysticflounder/equational-magma-theorems

#### Rule Coverage Analysis

**Discoverer**: Arjun Garg

- Developed a deterministic program to test decision rules in Cheatsheets
- Found rules cover **85.72%** on the complete 22 million implication graph, with **99.93%** precision
- On Hard3, rules cover 18.8%; theoretical upper bound is 66.75%
- Through prompt optimization, worst-case rule hit rate improved from 16.7% to **81%** (9x improvement)
- Open-source tools: https://github.com/arjun2garg/SAIR-Math-Distillation

### 3.6 Terence Tao's AlphaEvolve Predictors

On March 17, Terence Tao disclosed an important background: one month before the competition launched, he had already developed two Python predictors using **AlphaEvolve** to assist in generating "difficult" datasets.

**Terence Tao's original statement**:

> "A month ago, I used AlphaEvolve to develop two predictors (in Python) for this challenge, which had good success rate on random instances of the problem, and were used to help develop the 'hard' sets. The two predictors can be found here [predictor1.py] and here [predictor2.py]. It appears that the current cheatsheets are already well surpassing the capability of these initial predictors, so we will have to work harder to create a more meaningfully challenging evaluation set for this Stage 1 competition!"

These two predictors are foundational tools for constructing difficult sets, and the code is open source:
- predictor1.py: https://github.com/teorth/equational_theories/blob/main/scripts/predictor/predictor1.py
- predictor2.py: https://github.com/teorth/equational_theories/blob/main/scripts/predictor/predictor2.py

Heath Sanchez immediately asked whether this method could generalize:

> "This is absolutely fascinating if it generalizes. Have you tested this on larger terms, different generators, families not seen in evolution or even just a few hand-crafted adversarial cases? Would love to know if this is evidence of a universal implication method or if it's just learned the dataset geometry extremely well."

Adam Betka also offered profound observations on dataset distribution:

> "How in practice does the process of curating the hardest possible and widely distributed dataset from all 22 million magmas looks like? My GPT cheatsheets definitely do not generalize very well across the datasets (hard1, hard2 and the list from Teorth) yet in each of them they are easily findable, and the model can saturate the benchmark. There is something weird going on with the distribution of hard magmas."

This discussion revealed a deeper issue in the competition: **Is the difficult set distribution sufficiently diverse?** If hard Magma distributions have structural "clustering," a Cheatsheet that saturates cluster A may completely fail on cluster B.

### 3.7 Het S's Problem Space Visualization

Community member Het S (background in CS/ML rather than pure mathematics) independently conducted highly creative work—mapping the Magma implication problem to high-dimensional space for 3D visualization.

**Het S's methodology**:
- Computed "fingerprints" for each equation pair across 150 randomly generated Magmas (sizes 2, 3, 4)
- Mapped fingerprints to 50-dimensional space
- Used clustering algorithms to group, then projected to 3D space

**Het S's original comments**:

> "I've been having some fun trying to visualize/group the problems. This is the 1k normal set, I 'fingerprint' the equations using a set of 150 randomly generated magmas with s2, s3, and s4. Once I do that it combines and maps it into 50 dim space, clusters it, and then projects it to 3d space for visualization."

> "I also very much feel out of my depth cause majority of my background is CS/basic ML, not abstract mathematics lol. I love data vis/data competitions though."

> "I had a theory that if you could track the progress of the accuracy through the clusters you could literally 'see' blind spots in the LLM's knowledge."

Het S further developed interactive 3D visualization (navigable, resembling neuron connections), sharing HTML files for community download. They also marked "boundary" regions in yellow (expected to be harder problems) and central regions in purple/black (expected to be easier problems).

**Adam Betka's reaction**:

> "That's quite beautiful. If I give you a new magmas dataset would you be able to visualize its distribution too? I have been working on curating a 'super hard' yet maximally distributed dataset of magmas for evals."

**YZ's comment**:

> "@Het S this is super cool! It would be great to have an online visualization to dive in haha."

While this work did not directly produce competition submissions, it provided a completely new perspective for understanding problem space structure—if accuracy across different clusters could be tracked, LLM knowledge blind spots could literally be "seen."

### 3.8 Community Infrastructure and Collaboration Discussions

During the competition, the community engaged in extensive discussions around evaluation infrastructure, with the following topics being particularly critical:

#### 3.8.1 Evolution of Submission Format

Initially, the competition required Cheatsheet-only submissions (≤10KB), with SAIR providing fixed templates. However, the community strongly requested greater flexibility:

**Heath Sanchez's request**:

> "Having 'Your task is to determine whether Equation 1 implies Equation 2' at the beginning of the prompt is also pushing the model toward 'solve this instance now' instead of processing through my hard modular execution boundaries in chronological stages. The pipeline idea is working well, so a template that has nothing before the Cheatsheet would be ideal for me."

**Garbid's request**:

> "Would it be possible to add a completely clean template with no additions before or after the cheatsheet? The current Free-form prompt still appends a line at the end. Full control over what the model sees would allow testing approaches that require precise prompt structure."

**Baptiste Polchlopek's pragmatic view**:

> "I believe that, seeing all the community improvements, they are opening them up for exploration at the start. Then, I assume that half-way/near the end, one template will be chosen/communicated for Stage 1 eval."

On March 19, YZ officially announced a major rule change:

> "We will no longer plan to restrict Stage 1 to a fixed prompt template. Instead, participants will submit a **complete prompt**: prompt template + cheatsheet together."

This gave participants full control over prompt format, but they were responsible for ensuring parseable output.

#### 3.8.2 Calls for Evaluation Transparency

The community raised multiple demands for evaluation process transparency:

**Gan W.'s suggestions**:

> "It would be great if the competition could release the exact prompt and inference setting (model, provider, decoding parameters) used for LLM-as-Judge and also the parser used to parse the judge's response. This way we could actually reproduce the evaluation pipeline ourselves and get better feedback signals for our prompt search algorithms."

> "In fact just open sourcing the evaluation pipeline itself would be great."

**Garbid's creative suggestion**:

> "Would it be feasible to publish a simple leaderboard showing top 20 cheatsheet names and their accuracy on a hidden held-out set (say 500-1000 balanced problems), without revealing the actual cheatsheet contents? This would give everyone signal on whether current methods generalize to unseen problems."

**Terence Tao's response**:

> "We will be releasing some further details of the evaluation process closer to the submission deadline. On the other hand, we want to avoid competitors overfitting to the specific evaluation format, as a key objective of this competition is to obtain distilled insights about equational theories that are robust and applicable to a wide spectrum of evaluation procedures."

This reflects the organizers' delicate balance between **transparency** and **preventing overfitting**. On April 9, official Stage 1 evaluation code and model configurations were finally released (see Appendix B).

#### 3.8.3 Dataset Quality and Label Verification

Paul Buchanan's team found potential label errors in the hard2 dataset, verified by exhaustively checking all 2nd and 3rd-order Magma tables:

> "We have found computational evidence that two problems in the hard2 dataset may have incorrect ground-truth labels... We were unable to find a counterexample at any size we could check. We believe this problem is TRUE and the dataset label is incorrect."

Jose Brox independently verified the correct labels using Mace4:

> "I have also independently checked them with Mace4. There are countermodels of size 5 for the first implication and of size 8 for the second one."

This incident demonstrated the community's rigorous attitude toward data quality. YZ confirmed all labels were correct, providing links to ETP's Lean proofs.

---

## IV. Playground and Evaluation Infrastructure

### 4.1 SAIR Playground

| Feature | Specification |
|:--------|:--------------|
| URL | https://playground.sair.foundation |
| Inference Provider | OpenRouter |
| Daily Free Quota | 10 credits (≈$0.10) |
| Credit Reset | Daily at 00:00 UTC |
| Prompt Customization | ✅ Full free-form templates |
| Credit Reservation | Displayed credits are pre-authorization; actual charges are lower |

### 4.2 Community-Discovered Technical Issues

| Issue | Description | Impact |
|:------|:------------|:-------|
| Dataset duplication | 200 problems in Hard set contain only 69 unique | Fixed; hard1/hard2 released |
| Cheatsheet editing "contamination" | Editing existing sheets carries residual from old versions | Recommendation: always create new sheets |
| Gemma-4 reasoning silent failure | Reasoning mode consumes all tokens but outputs nothing | Gemma reasoning mode disabled |
| LLM temporal variability | Same Cheatsheet produces significantly different results at different times | Recommendation: run evaluation 3 times |
| Playground credit reservation | Displayed charges much higher than actual | Difference refunded |
| Llama reasoning mode | Llama's reasoning causes unstable output | Disabled in official evaluation |
| Groq XML wrapping | Outputs wrapped in XML tags when using Bedrock | Parser update needed |

### 4.3 Public Dataset Overview

| Dataset | Size | Source | TRUE/FALSE |
|:--------|:-----|:-------|:-----------|
| Normal | 1000 problems | SAIR | ~50/50 |
| hard1 | 69 problems | Original hard deduplicated | 24 TRUE / 45 FALSE |
| hard2 | 200 problems | Newly generated | 100/100 |
| hard3 | 400 problems | Newly generated | ~50/50 |
| SAIRCommunityBench v1 | 200 problems | Curated by Adam Betka | 100/100 |
| hard5 | 654 problems | Curated by Adam Betka | ~50/50 |
| ETP Hardest Implications | ~130 pairs | Curated by Terence Tao | Mixed |

---

## V. Participant Ecosystem and Collaboration Patterns

### 5.1 Active Participant Profiles

| Participant | Role/Background | Key Contributions |
|:------------|:----------------|:------------------|
| **Terence Tao** | Co-organizer, Fields Medalist | Published baselines, shared hardest implications, responded to community questions |
| **YZ** | SAIR Organizer | Operated Playground, updated rules, integrated community feedback |
| **Adam Betka** | Independent Researcher | First to saturate benchmark, released SAIRCommunityBench, algebraic Cheatsheet methods |
| **Heath Sanchez** | Mathematical Physicist | "Distillation vs. overfitting" analysis, FALSE-first strategy, checker paradigm |
| **Stacey Szmy** | AI Systems Builder | ZPYPIPE system, parser mechanism analysis, Playground testing strategies |
| **Gan W.** | AI Researcher | Generalization analysis, evaluation pipeline suggestions, high-generalization Cheatsheet |
| **Adam McKenna** | Independent Researcher | Spine Isolation Theorem, rule coverage analysis, multi-model equilibrium optimization |
| **Baptiste Polchlopek** | AI Researcher | Dataset quality audit, statistical rigor, train/validation methodology |
| **Arjun Garg** | Independent Researcher | Rule enforcement rate analysis, open-source evaluation tools |
| **Rhys Edwards** | AI Enthusiast | VERDICT position analysis, API system prompt suggestions |

### 5.2 Community Collaboration Mechanisms

- **SAIR Contributor Network**: Cheatsheet sharing platform with automated baseline testing and leaderboard display
- **Citation Mechanism**: Must cite when using others' Cheatsheets; citation count contributes to contribution scoring
- **Open Source Culture**: Most participants willing to share code and experience after Stage 1
- **GitHub Collaboration**: Multiple teams open-sourced evaluation tools and datasets

---

## VI. Key Controversies and Open Questions

### 6.1 "Do Universal Rules Exist?"

**Gan W.'s question**:
> "Are we in a universe where 'there exists a universal set of rules that governs all hard problems,' or 'there is no silver bullet'?"

If hard problems are each unique and require different techniques, leaderboards may become competitions of luck—whose Cheatsheet happens to cover more specific problems in the private test set.

**Heath Sanchez's response**:
> "The core of the competition is not about performing well on the most common implications, but testing whether true useful mathematical reasoning can be distilled into compact form. Hard problems are the most important probes—they reveal whether a Cheatsheet contains truly transferable reasoning rather than broad average-case heuristics."

### 6.2 Evaluation Fairness

Concerns raised by the community:

1. **Model Selection**: Llama-3.3-70B is generally considered a "drag"—forcing participants to sacrifice more elegant mathematical reasoning for it
2. **Token Limits**: The 8,192 token limit transforms the competition from "teaching models to solve math problems" into "preventing reasoning models from overthinking"
3. **Provider Instability**: OpenRouter's backend routing may cause the same prompt to perform differently at different times
4. **Evaluation Set Composition**: Is 2/3 hard problems reasonable? Gan W. suggested increasing the proportion of normal problems to reflect true generalization

### 6.3 True Distillation vs. Overfitting

The competition name "Distillation" implies some form of knowledge extraction, but the community observed:

- Many successful Cheatsheets are essentially **rule combinations tuned for dataset distribution**
- Adam Betka's algebraic Cheatsheets barely generalize across datasets
- Heath Sanchez noted: "Memorizing 'what it looks like' ≠ understanding 'why it must be so'"

**Possible Solution Directions**:
- Symbolic regression methods
- Mining true structural invariants from implication graphs
- SINDy (Sparse Identification of Nonlinear Dynamics)-like methods

### 6.4 The Boundaries of LLMs' Mathematical Capabilities

The deeper problem revealed by the competition:

> **Do small models possess mathematical capabilities far beyond what benchmark tests show?**

Adam Betka's bold assertion:
> "Many capabilities (including poetry, and also mathematics) are **representation-gated** in older/smaller models. With the right representation, these models can solve far more complex mathematical problems than top AI labs' benchmarks suggest."

Evidence: Llama-3.1-**8B** (8 billion parameters) with the right Cheatsheet achieves 77% on hard2—better than trillion-parameter models without any Cheatsheet.

---

## VII. Implications for AI Mathematical Reasoning

### 7.1 Core Findings Summary

| Finding | Significance |
|:--------|:-------------|
| 10KB Cheatsheet can improve LLMs from ~47% to ~99% | Knowledge distillation is extremely effective for mathematical reasoning |
| 8B parameter model can achieve 77% with correct representation | Model's "apparent capability" far below "latent capability" |
| Different models require fundamentally different prompt styles | No "universal prompt"—adaptation required |
| Programmatic checker > free reasoning agent | LLM as execution tool superior to autonomous thinker |
| Generalization is the greatest challenge | Current methods are more overfitting than true distillation |
| Finding counterexamples ≠ correct judgment | LLMs lack "metacognition"—trusting their own computational results |

### 7.2 Recommendations for Future Research

1. **Mechanistic Interpretability Research**: Does the Cheatsheet change the model's internal representations, or merely change routing from representation to output?
2. **Cross-Model Generalization**: Is there a prompt representation that achieves consistently good results across most open-source models?
3. **Automatic Cheatsheet Generation**: Can methods like AlphaEvolve automatically discover efficient rules?
4. **From Routing to Reasoning**: Stage 2's key challenge—can we evolve from "judging TRUE/FALSE" to "generating formal proofs"?
5. **Larger Equations**: Implication problems on 5th-order or higher equations may offer broader research space

---

## VIII. Special Topic: Manuel Cázares' Paper — "Less Is More: Cognitive Load and the Single-Prompt Ceiling in LLM Mathematical Reasoning"

### 8.1 Paper Overview

Manuel Israel Cázares (Bytepro AI, Mexico) systematically experimented with 45+ prompt variants during the competition and wrote a 17-page research paper titled "Less Is More: Cognitive Load and the Single-Prompt Ceiling in LLM Mathematical Reasoning." This is one of the most rigorous academic studies from the competition.

**Paper Core Parameters**:

| Dimension | Specification |
|:----------|:--------------|
| Experiment Duration | 5 weeks (through April 20, 2026) |
| Prompt Variants | 45+ (AN1 to AN45d) |
| Size Range | 0 bytes (baseline) to 4,878 bytes |
| Evaluation Datasets | normal, hard1, hard2, hard3 (1000+ problems total) |
| Test Models | gpt-oss-120b, Llama 3.3 70B, Gemma 4 31B |
| Additional Verification | DeepSeek V3.2 (n=10) |

### 8.2 Core Finding: The Single-Prompt Ceiling

The paper's central concept is the **"Single-Prompt Ceiling"**—more precisely, Cázares calls it the **Empirical Saturation Region**: a region where accuracy improvement becomes unstable and cannot generalize across problem distributions.

**Key Data**:
- No-Cheatsheet baseline (gpt-oss-120b, hard3): **59.75%** (95% CI: [54.9%, 64.4%]), True recall 82.6%, False recall only 38.0%
- Best local result (AN45c, n=400): **79.25%** (95% CI: [75.0%, 82.9%]), True recall 95.9%, False recall 63.4%
- Improvement: **+19.5 percentage points**

**But shockingly**: AN45c scores only **55.5%** on the SAIR official benchmark—0.8 percentage points *below* the no-Cheatsheet baseline (56.3%)!

### 8.3 Three Mechanisms Behind the Ceiling

Cázares identified three mechanisms causing the saturation region:

**Mechanism One: Limitations of Mathematical Undecidability**
TRUE cases require proving no counterexamples exist—including infinite Magma structures—and no finite prompt can completely encode such knowledge.

**Mechanism Two: Cognitive Collapse in Complex Rule Systems**
Prompts exceeding 2KB cause True recall to drop to 0% on Llama 3.3 70B. Specific examples:
- AN3c (4,306-byte Block system): 78.3% on hard1, but outputs all FALSE on Llama; True recall = 0%
- AN19c (only 289 bytes): True recall 37.5%, False recall 80.8% on Llama—the only variant maintaining balance on Llama

**Mechanism Three: Prompt Ordering Effects**
- AN38 places counterexample tables first: True recall 78.5%, False recall 65.4%
- AN45c places trivial Magma checks first: True recall 95.9%, False recall 63.4%
- These two variants differ by **only rule ordering**, producing a 7.5 percentage point difference (95% CI non-overlapping)

### 8.4 Merge Mode and Router Hypothesis

**Merge Mode: avg(A,B), not max(A,B)**

A key finding by Cázares is that merging results from two complementary prompts does not take the best of both, but converges toward arithmetic average:

| Variant | True Recall | False Recall | Size |
|:--------|:-----------|:-------------|:-----|
| AN35 (FALSE expert) | 58.3% | 84.6% | 1,545 bytes |
| AN35b (TRUE expert) | 79.2% | 65.4% | 1,802 bytes |
| **Arithmetic average** | 68.8% | 75.0% | — |
| **Merged result AN38** | **70.8%** | **76.9%** | 1,776 bytes |

Three subsequent merge attempts (AN45d/e/f) all reproduced this "averaging" pattern.

**Router Hypothesis**:

Cázares proposes that LLMs' behavior on this task is not symbolic theorem proving, but **heuristic classification of algebraic structure patterns**. The prompt encodes a decision boundary over structural features (variable repetition, nesting depth, singleton forcing), not a proof system.

> "The prompt encodes a decision boundary over structural features — variable repetition, nesting depth, singleton forcing — rather than a proof system. We term this the **router hypothesis**: the model routes problems to cached structural patterns rather than deriving answers compositionally."

### 8.5 Distribution Mismatch Failure Mode

The paper documents a critical failure mode:

- **AN3c** achieves 78.3% on hard1 (35% TRUE, False-heavy distribution)
- The same prompt achieves only 60.0% on hard2 (50/50 balanced distribution)—only 10 percentage points above random guessing
- Reason: Block 1 rules misclassify 51% of TRUE problems as FALSE

**Cross-Distribution Trade-Off Surface**:

Cázares analyzed 52 public submissions on the SAIR Contributor Network, finding:
- **Only one submission** (Arjun Garg's bank lookup v8) exceeds 65% on both hard2 and hard3
- Betka's Cheatsheet achieves 99% on hard2 but only 56.3% on hard3 (-42.7pp)
- Submissions labeled "hard3 overfitted" achieve 81.3% on hard3 but only 51.0% on hard2 (-30.3pp)

**Core Conclusion**: Within the empirical saturation region, optimizing performance on one distribution **sacrifices** performance on complementary distributions.

### 8.6 Practical Implications

1. **Simple > Complex**: AN38 (1,776 bytes) produces a robust +5.6pp improvement on the official benchmark (65.3% vs baseline 59.75%), while more complex AN45c (2,252 bytes) falls below baseline
2. **AN19c is the model-agnostic minimum effective unit**: Only 289 bytes, remains competitive across all three models (GPT 62%, Llama 60%, Gemma 55%)
3. **DeepSeek V3.2 cost efficiency**: AN19c + DeepSeek achieves 80% (n=10), costing approximately $0.0008 per problem—an order of magnitude cheaper than gpt-oss-120b
4. **Token limits are critical**: Gemma 4 achieves only 50% at 2,048 tokens (outputs all TRUE), but reaches 85% at 8,192 tokens
5. **Theoretical frameworks hurt**: All four variants introducing explicit mathematical reasoning frameworks (AN39/40/41/42) fall below baseline—the model "fabricates" plausible-sounding arguments to support TRUE within structured frameworks, even when counterexamples exist

> The paper's core lesson: "What improves performance is not teaching the model new mathematical knowledge, but controlling the order in which the model applies existing mathematical knowledge. In formal reasoning, as in engineering: fewer, well-structured instructions outperform more."

---

## IX. Stage 2 Outlook

| Dimension | Known Information |
|:----------|:-----------------|
| Start Date | May 1, 2026 |
| Registration | Pre-registration open |
| Model Selection | Plans to allow participants to choose models (DeepSeek-V4, Kimi, Gemma 4, etc.) |
| Cheatsheet Size | Will exceed 10 KB |
| Tool Usage | Code execution may be allowed |
| Output Requirement | Lean 4 formal proofs |
| Stage 1 Cheatsheets | Will be public and may be used as reference |

---

## Appendix A: Key Resource Links

| Resource | Link |
|:---------|:-----|
| SAIR Playground | https://playground.sair.foundation |
| Official Benchmark | https://benchmark.sair.foundation |
| Contributor Network | https://competition.sair.foundation/contributor-network |
| Official Evaluation Code | https://github.com/SAIRcompetition/equational-theories-stage1-judge |
| Community Dataset (GitHub) | https://github.com/SAIRcompetition/equational-theories-community |
| HuggingFace Dataset | https://huggingface.co/datasets/SAIRfoundation/equational-theories-selected-problems |
| ETP Project | https://teorth.github.io/equational_theories/ |
| Spine Isolation Theorem Preprint | https://zenodo.org/records/19380600 |
| Adam Betka's Community Benchmark | https://github.com/SAIRcompetition/equational-theories-community |
| Arjun Garg's Open Source Tools | https://github.com/arjun2garg/SAIR-Math-Distillation |
| Terence Tao's AlphaEvolve Predictor 1 | https://github.com/teorth/equational_theories/blob/main/scripts/predictor/predictor1.py |
| Terence Tao's AlphaEvolve Predictor 2 | https://github.com/teorth/equational_theories/blob/main/scripts/predictor/predictor2.py |
| Cázares Paper Open Source Code | https://github.com/israelcazares/sair-prompt-engineering |

---

## Appendix B: Final Evaluation Configuration Details

```json
// evaluation_models.json
[
  {
    "model": "openai/gpt-oss-120b",
    "max_tokens": 8192,
    "temperature": 0.0,
    "seed": 0,
    "reasoning": {"effort": "low"},
    "provider": {"order": ["Groq"], "quantizations": ["fp8"]}
  },
  {
    "model": "meta-llama/llama-3.3-70b-instruct",
    "max_tokens": 8192,
    "temperature": 0.0,
    "seed": 0,
    "reasoning": {"effort": "none"},
    "provider": {"order": ["AkashML"], "quantizations": ["fp8"]}
  },
  {
    "model": "google/gemma-4-31b-it",
    "max_tokens": 8192,
    "temperature": 0.0,
    "seed": 0,
    "reasoning": {"effort": "none"},
    "provider": {"order": ["Novita"], "quantizations": ["bf16"]}
  }
]
```

---

*Report generated: 2026-04-28 (v2 expanded version)*
*Data cutoff: 2026-04-27 (last message in Zulip channel)*
*New in this version: Section 3.6-3.8 community discussion originals, Section IX Manuel Cázares paper special topic*
*English translation: 2026-04-30*
