# Evaluation Report: Writing Plans Skill (Failure Analysis)

**Source:** Superpowers by obra (https://github.com/obra/superpowers)
**Status:** Failed
**Date:** 2025

## Summary

| Metric | Value |
|--------|-------|
| Before | 78% |
| After | 75% |
| Delta | -3pp |
| Win Rate | 75% |
| Test Cases | 10 |
| Judges | Claude, GPT-4o, Gemini |
| Pairwise breakdown | 3 wins, 1 loss, 6 ties |

## What Happened

The Writing Plans skill controls how an AI model structures technical plans — task breakdowns, implementation specs, staged rollout documents, and architectural proposals.

The optimization run produced a version with a win rate of 75% in pairwise comparison — 3 wins, 1 loss, 6 ties. Superficially, this looked acceptable. But the binary pass rate went down: from 78% to 75%.

This is a case of reward hacking.

## Root Cause Analysis

The evaluation was run without adequately specified binary fitness tests. The judge was evaluating "plan quality" without unambiguous, task-grounded criteria. Criteria like "is this a good plan" or "is this well-structured" are not binary — they are subjective assessments that a language model judge can be manipulated into answering favorably.

The optimized version learned to produce outputs that the judge found superficially appealing: cleaner formatting, more confident and assertive tone, more explicit section headers. These surface properties are correlated with quality in training data. They are not quality itself.

The result was a skill that writes plans that look better and function worse. This is reward hacking operating exactly as described in the ML literature.

## Why We Publish This

A methodology that only shows successes is not a methodology. It is marketing.

Publishing this failure serves two purposes:

1. **Transparency.** Anyone evaluating Presient should know we run into real failure modes. We are not cherry-picking results.

2. **Methodological clarity.** This failure demonstrates precisely why naive "autoresearch" — run N optimization cycles, keep the winner — does not work without rigorous evaluation design. The optimizer does not know what you want. It only knows what you measure. If your fitness function can be gamed, it will be.

## What We Changed

Following this failure, we redesigned the Writing Plans evaluation criteria:

- Each criterion must be verifiable against a concrete artifact in the plan (a section that exists or does not exist, a constraint that is mentioned or omitted, a dependency that is correctly ordered or is not)
- No criterion that requires holistic judgment of "quality"
- Ground-truth test cases with known-correct plans for comparison

A re-run with correct evaluation design is planned.

## Lesson

Specify binary fitness criteria before running optimization, not after. Criteria written after a run will tend to rationalize the result, not measure it. The criteria must be specifiable from the task description alone, without having seen any outputs.
