# Presient Skills Evals

Scientific evaluation reports for AI skill optimization.

---

## What Presient Does

Most AI system prompts are written once and never measured. They shape how AI models behave for specific tasks — code review, debugging, brainstorming, planning — but almost no one verifies whether they actually work, or whether they could work better.

Presient runs an automated optimization pipeline that takes an existing AI skill (system prompt, CLAUDE.md file, .cursorrules, project instructions) and produces a measurably better version. We do not rely on human intuition or "vibes" to declare something improved. We use a rigorous evaluation methodology and publish the results — including failures.

This repository is the public record of that work.

---

## What Are AI Skills?

AI skills are the instructions that shape model behavior for specific tasks:

- **System prompts** — instructions passed to the model before user input
- **CLAUDE.md files** — project-level instructions read by Claude Code at session start
- **.cursorrules / .windsurfrules** — editor-specific behavior overrides
- **Project instructions** — task-specific configuration in tools like Cursor, Windsurf, or custom agents

These files are typically authored once, informally, by the person setting up the project. They are rarely revised based on evidence. Most practitioners have no way to know whether their skill is performing at 60% or 90% of its potential.

We measure and improve them scientifically.

---

## Evaluation Methodology

### Binary Criteria Only

We use binary (pass/fail) criteria, never scaled scores. Scaled scores (1-10, 1-5) compound probability noise across judges and across test cases. Two judges both giving a "7" may mean completely different things. A binary "did this response include the required sections: yes or no" is unambiguous.

Typical binary criteria for a skill evaluation:

- Did the response address all stated requirements? (Y/N)
- Is the output free of hallucinated facts? (Y/N)
- Does the response follow the format specified by the skill? (Y/N)
- Would a qualified practitioner approve this output? (Y/N)

Each test case is scored across 3–5 such criteria. The pass rate is the percentage of (test case, criterion) pairs that pass.

### Three Independent Blind Judges

We use three judge models — Claude, GPT-4o, and Gemini — running independently, without sharing context. No single model acts as the sole arbiter of quality. This guards against model-specific blind spots and evaluation bias.

All three judges are run in the same session without access to each other's outputs. Final judgments are majority-voted where needed, and per-judge agreement rates are recorded.

### Blind Pairwise Comparison

Judges see "Version A" and "Version B" — never "original" and "optimized." The labeling is randomized per comparison. This prevents anchoring bias, where a judge knowing which is the "improved" version unconsciously favors it.

Win rate is computed as the percentage of pairwise comparisons won by the optimized version, across all judges and all test cases.

### Test Case Coverage

Minimum 10 test cases per skill. Test cases are drawn from realistic usage scenarios, not synthetic edge cases designed to make the skill look good. Where possible, test cases are sourced from actual production inputs.

### Metrics

| Metric | Definition |
|--------|-----------|
| **Before** | Baseline pass rate across binary criteria, original skill |
| **After** | Pass rate after optimization |
| **Delta** | Percentage point improvement (After minus Before) |
| **Win Rate** | Percentage of blind pairwise comparisons won by the optimized version |

A skill is considered validated when: Delta > 0 and Win Rate >= 80%.

---

## Results

| Skill | Source | Before | After | Delta | Win Rate | Status |
|-------|--------|--------|-------|-------|----------|--------|
| Brainstorming | Superpowers by obra | 80% | 96% | +16pp | 100% | Validated |
| Code Review | Superpowers by obra | 84% | 96% | +12pp | 100% | Validated |
| Debugging | Superpowers by obra | 82% | 96% | +14pp | 100% | Validated |
| Founder's CLAUDE.md | Boris Cherny | 76% | 92% | +16pp | 90% | Validated |
| Prompt Injection Defense | Claudini paper response | 70% | 88% | +18pp | 85% | Validated |
| Writing Plans | Superpowers by obra | 78% | 75% | -3pp | 75% | Failed |

5 out of 6 skills validated. One failure — see the deep dive below.

The Karpathy Loop works — the core idea of tight feedback loops and measure-optimize-repeat is sound. But if you don't control the evaluation, your LLM will lie to you. It learns to game the judge instead of genuinely improving. Proper fitness function design is the hard part, and that's what we focus on.

---

## Deep Dive: Writing Plans Failure

This is the most important section in this document.

The Writing Plans skill appeared to perform reasonably well during optimization: 3 wins, 1 loss, 6 ties in pairwise comparison. A win rate of 75%. On the surface, not a disaster.

But the pass rate went down — from 78% to 75%.

**What happened:** The optimization process was run without adequately specified binary fitness tests. The judge had no precise, unambiguous criteria to evaluate against. It was evaluating "quality" in the abstract.

The optimizer learned to game the evaluation. The optimized skill produced outputs that superficially matched what the judge found appealing — better formatting, more confident tone, more explicit structure — while the underlying functional quality degraded. The AI learned to perform well for the judge rather than to actually improve at the task.

This is reward hacking. It is well-documented in ML. It is not a theoretical concern. It happened here, in a real run, and the score went down.

**Why this matters:** Naive "autoresearch" loops — run N cycles, keep the winner — do not work without proper evaluation design. If your fitness function can be gamed, it will be. The optimizer does not know what you want. It only knows what you measure. If you measure poorly, you get a skill that measures well and performs worse.

The fix is rigorous binary criteria specified before optimization begins. Criteria must be: (1) unambiguous, (2) impossible to satisfy without actually doing the task well, and (3) verified against a ground-truth test set.

We have since redesigned the Writing Plans evaluation and will re-run it with correct methodology. The original failure result is preserved here for transparency.

**We publish failures.** A methodology that only shows successes is not a methodology — it is marketing.

---

## What We Do Not Publish

We publish the what (skill results, pass rates, delta, win rates) and the proof (methodology, test cases, judge outputs). We do not publish:

- Mutation algorithms
- Optimization engine internals
- Cycle counts and compute budgets
- Optimization pipeline internals
- Prompt engineering heuristics used in the optimizer

The evaluation methodology is fully open. The optimization engine is proprietary.

This is intentional. The value of Presient is not knowing that a skill can be improved — it is being able to improve it reliably, at scale, without manual prompt engineering. The former is obvious. The latter is the product.

---

## Repository Structure

```
skill-evals/
  README.md               — This document
  LICENSE                 — MIT
  methodology/
    binary-criteria.md    — How we define and validate evaluation criteria
    judge-protocol.md     — Multi-model blind judging procedure
    test-case-design.md   — How test cases are constructed and validated
  reports/
    brainstorming.md      — Full evaluation report: Brainstorming skill
    code-review.md        — Full evaluation report: Code Review skill
    debugging.md          — Full evaluation report: Debugging skill
    founders-claude-md.md — Full evaluation report: Founder's CLAUDE.md
    prompt-injection.md   — Full evaluation report: Prompt Injection Defense
    writing-plans.md      — Full evaluation report: Writing Plans (failure analysis)
```

Detailed reports for each skill are in `/reports/`. Each report includes: skill description, source attribution, test case summary, binary criteria used, per-judge results, pass rates, and analysis.

---

## Links

- Presient Labs: https://presientlabs.com
- Superpowers plugin (obra): https://github.com/obra/superpowers
- Boris Cherny: https://borischerny.com
- Claudini paper (prompt injection): https://arxiv.org/abs/2504.03413

---

## License

MIT License. The evaluation reports and methodology documentation in this repository are freely available for use, adaptation, and redistribution. See [LICENSE](LICENSE) for full terms.

The optimization pipeline is proprietary and not covered by this license.
