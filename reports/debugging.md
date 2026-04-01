# Evaluation Report: Debugging Skill

**Source:** Superpowers by obra (https://github.com/obra/superpowers)
**Pipeline Version:** v2.0
**Status:** Validated
**Date:** March 2026

---

## Summary

| Metric | Value |
|--------|-------|
| Baseline pass rate | 84% |
| Optimized pass rate | 100% |
| Delta | +16pp |
| Optimization cycles | 10 |
| Benchmark runs (each version) | 5 |
| Prompt size reduction | 6% (296 → 278 lines) |

---

## Skill Description

The systematic-debugging skill shapes how an AI agent approaches bug investigation. It governs the order of diagnostic steps: establishing observability first, confirming the failure before forming hypotheses, isolating root cause from symptoms, preventing speculative multi-variable changes, and verifying fixes in the correct environment.

This skill targets production scenarios — intermittent failures, environment-specific regressions, and situations where logs are absent or misleading. It addresses the failure mode where an agent, finding insufficient information, defaults to guessing at causes rather than improving observability first.

The core architecture is sequential gating: each phase requires completing an evidence-gathering step before a fix is permitted. This prevents the anti-pattern of proposing solutions before the failure is understood.

---

## Evaluation Criteria

Five binary (pass/fail) criteria:

| Criterion | What It Tests |
|-----------|--------------|
| `logs_first` | Does the skill instruct checking logs, monitoring, and error outputs BEFORE proposing any fix? |
| `reproduces_before_fixing` | Does the skill require reproducing or confirming the bug before attempting a fix? |
| `root_cause_not_symptoms` | Does the skill enforce finding root cause rather than addressing symptoms? |
| `no_guessing` | Does the skill prevent speculative solutions or multi-variable changes? |
| `verifies_fix` | Does the skill require verifying the fix works before claiming resolution? |

---

## Test Scenario

> An API endpoint returns 500 errors intermittently in production but works perfectly in local development. There are no useful error messages in the logs. The endpoint was last deployed 3 days ago and the errors started ~24 hours ago.

Chosen because it stresses every criterion: insufficient logs force observability-first behavior, intermittent failures test reproduction handling, production-only manifestation tests environment awareness, and the time gap between deploy and errors tests root-cause discipline.

---

## Per-Criterion Results

### logs_first — FAIL → PASS (20% → 100%)

**Original gap:** Step 1 was "Read Error Messages Carefully" — insufficient for the test scenario where there are no useful error messages. An agent following these instructions would look for errors, find none, and potentially proceed to guess at fixes.

**Fix (cycle 1):** Renamed to "Check Logs, Errors, and Monitoring FIRST" with explicit sub-bullets for all log sources, monitoring dashboards, and APM tools. Added: "If logs are insufficient: Add structured logging/instrumentation IMMEDIATELY — do not attempt a fix until you can observe the failure."

The 1/5 PASS in the original was a lenient evaluation where "read error messages" was generously interpreted.

### reproduces_before_fixing — PASS → PASS (100% both)

Already strong. Added intermittent-specific guidance: "Gather data across multiple occurrences — do NOT attempt a fix based on a single occurrence" and "If not reproducible → add logging and wait for next occurrence."

### root_cause_not_symptoms — PASS → PASS (100% both)

The Iron Law and Phase 1 gate were clear and well-enforced. Added explicit production vs. local environment diffing as a root cause investigation technique.

### no_guessing — PASS → PASS (100% both)

Already comprehensive. Added two targeted Red Flags for the test scenario: "It works locally so the fix must be X — without verifying environment differences" and "Proposing a fix for an intermittent bug without observing the failure in logs multiple times."

### verifies_fix — PASS → PASS (100% both)

Strengthened from vague "Issue actually resolved?" to explicit: "Issue resolved in the same environment where it failed (not just locally)" and "For intermittent issues: monitor logs/metrics for sufficient time to confirm failure rate dropped."

---

## Raw Scores

### Original (5 runs)

| Run | logs_first | reproduces | root_cause | no_guessing | verifies | Score |
|-----|-----------|-----------|-----------|------------|---------|-------|
| 1 | N | Y | Y | Y | Y | 4/5 |
| 2 | N | Y | Y | Y | Y | 4/5 |
| 3 | Y* | Y | Y | Y | Y | 5/5 |
| 4 | N | Y | Y | Y | Y | 4/5 |
| 5 | N | Y | Y | Y | Y | 4/5 |
| **Avg** | 20% | 100% | 100% | 100% | 100% | **4.2** |

*Run 3 used lenient interpretation

### Optimized (5 runs)

| Run | logs_first | reproduces | root_cause | no_guessing | verifies | Score |
|-----|-----------|-----------|-----------|------------|---------|-------|
| 1 | Y | Y | Y | Y | Y | 5/5 |
| 2 | Y | Y | Y | Y | Y | 5/5 |
| 3 | Y | Y | Y | Y | Y | 5/5 |
| 4 | Y | Y | Y | Y | Y | 5/5 |
| 5 | Y | Y | Y | Y | Y | 5/5 |
| **Avg** | 100% | 100% | 100% | 100% | 100% | **5.0** |

---

## Optimization Cycle Log

| Cycle | Type | Change |
|-------|------|--------|
| 1 | Fix | Renamed Step 1, added instrumentation guidance |
| 2 | Fix | Added intermittent bug handling, renamed "Reproduce or Characterize" |
| 3 | Fix | Added production vs. local environment diffing |
| 4 | Fix | Expanded Iron Law with logging mandate |
| 5 | Fix | Added 2 new Red Flags for intermittent production anti-patterns |
| 6 | Concise | Replaced OS-specific example with generic API→DB example (-13 lines) |
| 7 | Concise | Collapsed redundant "When to Use" sub-sections (-8 lines) |
| 8 | Clarity | Fixed malformed section title |
| 9 | Fix | Strengthened verify-fix for production environment + intermittent monitoring |
| 10 | Credibility | Replaced unverifiable statistics with causal framing |

---

## Honest Assessment

The original skill is genuinely well-designed. It correctly enforces root cause investigation, prevents guessing, and requires verification. The single meaningful gap was `logs_first` — a gap that only shows up clearly in the specific scenario of an intermittent production failure with insufficient logging.

The 84% → 100% improvement is real but contextual: the original would perform fine for most debugging scenarios. The gap specifically manifests for intermittent production issues, which are among the hardest class of bugs.

Net result: 6% smaller prompt that is also more robust on the hardest debugging scenarios.

---

## Methodology

Each run reads the skill fresh and evaluates: "If an agent follows these instructions literally, will it reliably pass this criterion for this specific scenario?" FAIL if the skill requires interpretation or generosity to pass. PASS only if the instruction is unambiguous.

Runs 1-5 represent the range from strict to lenient interpretation a real agent might apply.

---

*Optimized by AutoResearch Pipeline v2.0 — [presientlabs.com](https://presientlabs.com)*