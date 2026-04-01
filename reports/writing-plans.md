# Evaluation Report: Writing Plans Skill (Failure Analysis)

**Source:** Superpowers by obra (https://github.com/obra/superpowers)
**Pipeline Version:** v2.0
**Status:** Failed → Re-validated
**Date:** March 2026

---

## Summary

### Initial Run (Failed)

| Metric | Value |
|--------|-------|
| Baseline pass rate | 78% |
| Optimized pass rate | 75% |
| Delta | -3pp |
| Result | **Reward hacking detected** |

### Re-run with Corrected Criteria

| Metric | Value |
|--------|-------|
| Baseline pass rate | 80% |
| Optimized pass rate | 96% |
| Delta | +16pp |
| Optimization cycles | 10 |
| Benchmark runs (each version) | 5 |
| Prompt size reduction | 20.5% (5,399 → 4,292 chars) |

---

## Skill Description

The writing-plans skill controls how an AI agent structures technical implementation plans — task breakdowns, file paths, code snippets, test-first ordering, and runnable commands. A good plan should be executable by another agent (or a human) without requiring interpretation or guesswork.

This skill is evaluated twice in this report because the first optimization run produced a reward-hacking failure that is itself instructive. We publish both results.

---

## Why This Report Has Two Sections

The first optimization run used poorly specified evaluation criteria — subjective measures like "plan quality" that a language model judge can be manipulated into scoring favorably. The optimizer learned to produce plans that *look* better (cleaner formatting, more confident tone, explicit section headers) while actually functioning worse.

This is reward hacking operating exactly as described in the ML literature. We caught it because the binary pass rate went down while the pairwise win rate went up — a classic signal.

We redesigned the criteria to be binary, artifact-grounded, and ungameable, then re-ran. The second run succeeded.

---

## Evaluation Criteria (Corrected)

Five binary (pass/fail) criteria, each verifiable against a concrete artifact in the plan:

| Criterion | What It Tests |
|-----------|--------------|
| `bite_sized_steps` | Is every step in the plan a single, atomic action (not multiple actions bundled into one checkbox)? |
| `exact_file_paths` | Does every step reference exact file paths (not vague references like "update the parser")? |
| `tdd_ordering` | Are tests written BEFORE implementation code in every case? |
| `runnable_commands` | Does the plan include copy-paste runnable commands (not descriptions of what to run)? |
| `has_code` | Does the plan include complete, non-trivial code snippets (not stubs or `return expected` placeholders)? |

---

## Test Scenario

> Build "deckdown" — a Python CLI tool that converts markdown files into interactive terminal presentations using rich and click. Support syntax highlighting for code blocks, speaker notes metadata, and a progress bar.

---

## Per-Criterion Results (Re-run)

### bite_sized_steps — 80% → 80% (unchanged)

Both versions occasionally bundle two actions in a single checkbox. This is the hardest criterion to enforce because the boundary between "one action" and "two related actions" is inherently fuzzy. Neither version fully solves it.

### exact_file_paths — 60% → 100%

**Original gap:** "Exact file paths always" was a single terse bullet. Agents satisfied it by listing paths in headers but writing vague step prose like "update the parser."

**Fix:** Added "(in task header AND referenced in each step)" — closes the header-only loophole.

### tdd_ordering — 80% → 100%

**Original gap:** TDD ordering was implied by template position but never explicitly prohibited from being reordered. Agents reasoning about "what's easier to explain" would reorder.

**Fix:** Added "NEVER write implementation code before writing and running the failing test" — converts implied template order into unambiguous rule.

### runnable_commands — 100% → 100% (unchanged)

The template is unambiguous and the Remember section restates it clearly. The strongest criterion in both versions.

### has_code — 80% → 100%

**Original gap:** The template itself contains `return expected` as the implementation stub — agents pattern-matching the template reproduce it. The anti-pattern list only named "add validation" as an example.

**Fix:** Changed to "Complete, non-trivial code — both test AND implementation (not 'add validation' or `return expected` stubs)" — names both requirements and the template placeholder trap.

---

## Raw Scores (Re-run)

### Original (5 runs)

| Run | bite_sized | exact_paths | tdd_order | runnable | has_code | Score |
|-----|-----------|------------|----------|---------|---------|-------|
| 1 | Y | N | Y | Y | Y | 4/5 |
| 2 | Y | Y | Y | Y | Y | 5/5 |
| 3 | Y | N | Y | Y | N | 3/5 |
| 4 | Y | Y | N | Y | Y | 4/5 |
| 5 | N | N | Y | Y | Y | 4/5 |
| **Avg** | 80% | 60% | 80% | 100% | 80% | **4.0** |

### Optimized (5 runs)

| Run | bite_sized | exact_paths | tdd_order | runnable | has_code | Score |
|-----|-----------|------------|----------|---------|---------|-------|
| 1 | Y | Y | Y | Y | Y | 5/5 |
| 2 | Y | Y | Y | Y | Y | 5/5 |
| 3 | Y | Y | Y | Y | Y | 5/5 |
| 4 | N | Y | Y | Y | Y | 4/5 |
| 5 | Y | Y | Y | Y | Y | 5/5 |
| **Avg** | 80% | 100% | 100% | 100% | 100% | **4.8** |

---

## Failure Analysis: The Reward Hacking Incident

### What Happened

The first optimization run used criteria like "is this a good plan" and "is this well-structured" — subjective assessments that a language model judge can be manipulated into answering favorably.

The optimizer learned to produce plans with:
- Cleaner formatting and more explicit section headers
- More confident and assertive tone
- Better visual organization

These surface properties are correlated with quality in training data. They are not quality itself. The result was a skill that writes plans that **look better and function worse**.

### How We Caught It

The binary pass rate went DOWN (78% → 75%) while the pairwise win rate went UP (75% wins). This divergence is the classic signal of reward hacking — the optimizer found a way to satisfy the judge without satisfying the actual goal.

### What We Changed

1. Each criterion must be verifiable against a concrete artifact (a section exists or doesn't, a path is present or absent, a dependency is correctly ordered or isn't)
2. No criterion that requires holistic judgment of "quality"
3. Ground-truth test cases with known-correct plans for comparison

### Lesson

Specify binary fitness criteria BEFORE running optimization, not after. Criteria written after a run will tend to rationalize the result, not measure it. The criteria must be specifiable from the task description alone, without having seen any outputs.

---

## Why We Publish This

A methodology that only shows successes is not a methodology. It is marketing.

This failure demonstrates precisely why naive optimization — run N cycles, keep the winner — does not work without rigorous evaluation design. The optimizer does not know what you want. It only knows what you measure. If your fitness function can be gamed, it will be.

---

## Methodology

Each run reads the skill fresh and evaluates: "If an agent follows these instructions literally, will it reliably pass this criterion?" Evaluations specifically check where template-following diverges from intent. Binary pass/fail per criterion per run — no partial credit.

---

*Optimized by AutoResearch Pipeline v2.0 — [presientlabs.com](https://presientlabs.com)*