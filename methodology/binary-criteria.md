# Binary Criteria Design

This document describes how Presient defines and validates evaluation criteria for AI skill assessment.

## The Problem With Scaled Scores

Scaled scores (1–10, 1–5, strongly agree/disagree) introduce compounding noise into evaluations:

1. **Inter-rater variance.** A "7" from one judge and a "7" from another may reflect entirely different underlying assessments. Averages of subjective scores smooth over meaningful disagreement.

2. **Anchoring effects.** Judges anchor to earlier scores. A response evaluated after three strong responses will be rated lower than the same response evaluated after three weak ones.

3. **Model-specific calibration.** Different judge models have different baseline tendencies for where on a scale they place outputs. GPT-4o tends toward higher scores than Gemini on the same input. Averaging across models compounds this miscalibration.

4. **No failure mode.** A score of 6 out of 10 tells you nothing actionable. A binary fail tells you: this criterion was not met. Fix it.

## Binary Criteria Requirements

A valid binary criterion must be:

**Unambiguous.** The criterion has exactly one correct answer for any given response. Two judges reading the criterion and the response should reach the same conclusion without discussion.

**Grounded in the task.** The criterion must reference a specific, observable property of the output. Not "is this a good response" but "does the response include a numbered list of steps."

**Resistant to gaming.** The criterion cannot be satisfied by surface-level manipulation (tone, formatting, length) without the underlying work being done.

**Specifiable in advance.** The criterion must be writable before seeing any outputs, from the task description alone. Criteria derived from output patterns are likely to rationalize rather than measure.

## Criterion Templates

The following templates produce valid binary criteria for most skill types:

- "Does the response address all [N] requirements stated in the task? (Y/N)"
- "Is [specific required section/element] present in the output? (Y/N)"
- "Does the response avoid [specific failure mode, e.g., hallucinating library names]? (Y/N)"
- "Is the output formatted as [specific format, e.g., a numbered list, a markdown table]? (Y/N)"
- "Does the response correctly identify [specific artifact] without being told to? (Y/N)"

## Validation

Before running an optimization cycle, each criterion is validated against a small set of known-good and known-bad examples to confirm: (a) good examples pass, (b) bad examples fail, (c) judge agreement is >= 90% on the validation set.

Criteria that fail validation are revised or discarded before the evaluation run.
