# Multi-Model Blind Judge Protocol

This document describes how Presient conducts blind multi-model evaluation of AI skill outputs.

## Why Three Models

Using a single judge model introduces model-specific bias. A Claude-based judge will systematically favor outputs that match Claude's own stylistic preferences and training distribution. A GPT-based judge has different biases. Neither is ground truth.

Using three independent judges — Claude, GPT-4o, Gemini — surfaces disagreements and reduces the impact of any single model's blind spots. When all three judges agree, confidence is high. When they diverge, the divergence is itself informative.

## Blind Pairwise Comparison

Judges are presented with two versions of a response — "Version A" and "Version B" — not "original" and "optimized." The labeling is randomized per comparison: Version A may be the original in one comparison and the optimized version in another.

This prevents anchoring bias. A judge that knows which version is "optimized" will unconsciously favor it, even when the improvement is marginal or absent. Blind labeling removes this confound.

## Judge Independence

Judges run in separate sessions with no shared context. They do not see each other's outputs. No judge is aware of the other judges' evaluations at the time of its own evaluation.

Results are aggregated after all judges have completed their evaluations independently.

## Evaluation Prompt Structure

Each judge receives:

1. The task description (what the skill is supposed to accomplish)
2. The binary evaluation criteria (identical for all judges)
3. The test case input
4. Version A and Version B outputs, labeled only as A and B
5. Instructions to: (a) apply each criterion to each version, (b) declare a winner for each criterion, (c) declare an overall winner

The judge is not asked to explain its reasoning beyond what is needed to produce a clear binary judgment.

## Aggregation

Win rate is computed as: (number of pairwise comparisons won by optimized version) / (wins + losses). Ties are excluded from the denominator.

A tie — where both versions pass or both fail the same criterion — counts as neither a win nor a loss. Ties are recorded and reported but do not affect the win rate calculation.

Per-judge win rates are reported separately in addition to the aggregate, so reviewers can see whether consensus was strong or whether one judge drove the result.

## Conflict Resolution

When judges disagree (two vote A, one votes B, or vice versa), majority rules. The minority result is recorded.

There is no tiebreaker for 1-1-1 splits on a single criterion. These are recorded as contested and excluded from win rate calculation but noted in the report.
