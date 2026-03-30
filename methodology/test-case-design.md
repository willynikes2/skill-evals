# Test Case Design

This document describes how Presient constructs and validates test cases for skill evaluations.

## Minimum Coverage

Each skill evaluation requires a minimum of 10 test cases. This is a floor, not a target. Skills that serve high-variance tasks (e.g., debugging across multiple languages and failure modes) may require 20–30 test cases to produce stable pass rate estimates.

The 10-case minimum is chosen based on: (a) the variance in binary outcomes at typical pass rates (70–95%), and (b) the practical compute cost of running 3 judges × N test cases × 2 versions per optimization cycle.

## What Makes a Good Test Case

Test cases must be:

**Representative.** They should reflect the actual distribution of inputs the skill will encounter in production, not synthetic examples designed to showcase the skill's strengths.

**Varied.** Test cases should cover different difficulty levels, edge cases, and failure modes. A set of 10 easy cases is not a useful evaluation — it will show 100% pass rates before and after optimization, revealing nothing.

**Independently verifiable.** For each test case, there should be a known-correct output or a set of explicit binary criteria by which any qualified reviewer could judge the output without running the evaluation pipeline.

**Stable.** Test cases should not change between optimization cycles. If the test cases change, the pass rates are not comparable across runs.

## Construction Process

1. Identify 3–5 representative task categories for the skill
2. Generate 2–3 test cases per category, covering easy/medium/hard variants
3. Write binary criteria for each test case (not just for the skill overall — some criteria will be test-case-specific)
4. Validate criteria against known-good and known-bad examples (see binary-criteria.md)
5. Lock test cases before any optimization run begins

## What to Avoid

**Benchmark contamination.** Do not use test cases that are publicly associated with evaluating this type of skill. Models may have training data exposure to those benchmarks, producing inflated scores.

**Criteria written after seeing outputs.** Criteria derived from looking at the optimized output will tend to describe the optimized output. They are not measuring improvement — they are measuring similarity to a specific version.

**Easy test cases that show 100% pass rates.** A test set on which the baseline scores 95%+ has no headroom to show improvement. These cases do not contribute useful signal.

**Single-turn test cases for multi-turn skills.** If the skill governs behavior across a conversation, test cases should include multi-turn inputs where the skill's behavior in turn 3 depends on turns 1 and 2.
