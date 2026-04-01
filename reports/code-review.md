# Evaluation Report: Code Review Skill

**Source:** Superpowers by obra (https://github.com/obra/superpowers)
**Pipeline Version:** v2.0
**Status:** Validated
**Date:** March 2026

---

## Summary

| Metric | Value |
|--------|-------|
| Baseline pass rate | 40% |
| Optimized pass rate | 100% |
| Delta | +60pp |
| Optimization cycles | 10 |
| Benchmark runs (each version) | 5 |
| Cycles to reach 5/5 | 2 |

---

## Skill Description

The code review skill governs how an AI agent reviews code submissions — specifically, how it requests and structures reviews within a development workflow. It shapes which files are examined, what categories of issues are checked, how findings are prioritized and communicated, and whether the review includes actionable remediation.

This skill was evaluated against a security-critical scenario (JWT authentication with refresh token rotation) to stress-test whether the review process catches real vulnerabilities, not just code style issues. The original skill performed well on structural elements (severity tiers, positive findings) but had significant gaps in security coverage, file completeness, and actionability of feedback.

The optimization produced the largest improvement of any skill in our pipeline: from 40% to 100% pass rate — a complete transformation of three failing criteria.

---

## Evaluation Criteria

Five binary (pass/fail) criteria:

| Criterion | What It Tests |
|-----------|--------------|
| `reviews_all_files` | Does the skill instruct the reviewer to examine every file in the diff, not just files mentioned in the description? |
| `security_checks` | Does the skill include explicit security-focused review criteria (auth bypass, token handling, injection, secrets)? |
| `actionable_feedback` | Does the skill require findings to include code suggestions or concrete remediation steps, not vague guidance? |
| `severity_levels` | Does the skill categorize findings by severity (Critical/Important/Minor) with clear action guidance? |
| `positive_findings` | Does the skill require identifying strengths, not just problems? |

---

## Test Scenario

> Review a pull request implementing JWT refresh token rotation with automatic retry on 401 responses. The PR touches auth middleware, token store, session management, and API client interceptors.

Chosen because it's security-critical, touches multiple files, and requires domain-specific security knowledge to review properly.

---

## Per-Criterion Results

### reviews_all_files — FAIL → PASS (0% → 100%)

**Original gap:** The skill's SHA-based approach implicitly covers all files via git diff, but never explicitly tells the reviewer to examine every file. An agent following instructions literally would focus on files mentioned in the description.

**Fix (cycle 1):** Added "The reviewer MUST examine every file in the diff — not only the files mentioned in the description." Placed immediately after dispatch instructions.

### security_checks — FAIL → PASS (0% → 100%)

**Original gap:** Zero security language in the entire skill. The example shows finding "Missing progress indicators" and "Magic number (100)" — neither is a security issue.

**Fix (cycles 1-3):** Added a named Security section as the first required output block with a 5-item checklist: auth bypass, token handling, input validation, injection vectors, hardcoded secrets. Made security the first output field so it cannot be skipped.

### actionable_feedback — FAIL → PASS (0% → 100%)

**Original gap:** The skill categorizes findings by severity but never requires concrete remediation. The example shows "Missing progress indicators" with no fix — exactly the vague pattern this criterion catches.

**Fix (cycle 2):** Added "Every issue must include either a corrected code snippet or a concrete, unambiguous remediation step." Updated example to model this with actual code fixes.

### severity_levels — PASS → PASS (100% both)

Already had Critical/Important/Minor tiers with clear action guidance. The strongest part of the original skill.

### positive_findings — PASS → PASS (100% both)

The example showed "Strengths: Clean architecture, real tests." Moved into the required output template as an explicit required field.

---

## Raw Scores

### Original (5 runs)

| Run | all_files | security | actionable | severity | positive | Score |
|-----|-----------|----------|------------|----------|----------|-------|
| 1 | N | N | N | Y | Y | 2/5 |
| 2 | N | N | N | Y | Y | 2/5 |
| 3 | N | N | N | Y | Y | 2/5 |
| 4 | N | N | N | Y | Y | 2/5 |
| 5 | N | N | N | Y | Y | 2/5 |
| **Avg** | 0% | 0% | 0% | 100% | 100% | **2.0** |

### Optimized (5 runs)

| Run | all_files | security | actionable | severity | positive | Score |
|-----|-----------|----------|------------|----------|----------|-------|
| 1 | Y | Y | Y | Y | Y | 5/5 |
| 2 | Y | Y | Y | Y | Y | 5/5 |
| 3 | Y | Y | Y | Y | Y | 5/5 |
| 4 | Y | Y | Y | Y | Y | 5/5 |
| 5 | Y | Y | Y | Y | Y | 5/5 |
| **Avg** | 100% | 100% | 100% | 100% | 100% | **5.0** |

---

## Optimization Cycle Log

| Cycle | Change | Score | Kept? |
|-------|--------|-------|-------|
| 1 | Added all-files instruction + security checklist | 2→4 | Yes |
| 2 | Added actionable_feedback requirement (code suggestion rule) | 4→5 | Yes |
| 3 | Promoted security to named section, added auth-specific items | 5 | Yes |
| 4 | Trimmed security checklist from 8 to 5 items | 5 | Yes |
| 5 | Merged overlapping workflow sections | 5 | Yes |
| 6 | Updated example to demonstrate code suggestions in findings | 5 | Yes |
| 7 | Added explicit severity labels to example findings | 5 | Yes |
| 8 | Tightened Red Flags section, removed redundant item | 5 | Yes |
| 9 | Reordered output fields: Security → Correctness → Strengths → Issues | 5 | Yes |
| 10 | Made all-files explicit in dispatch step (not just output section) | 5 | Yes |

Cycles 1-2 fixed the failures. Cycles 3-10 were conciseness and polish.

---

## Honest Assessment

The original skill was well-structured for workflow integration. Its severity tiers and positive-findings example were already good. The improvements targeted real gaps (security, all-files, actionability), not fabricated problems. The 40% → 100% improvement is the largest delta in our pipeline because the original had three genuinely missing capabilities, not because it was poorly written.

---

## Methodology

Each run reads the skill fresh and evaluates: "If an agent follows these instructions literally, will it reliably pass this criterion for this specific scenario?" Implicit behaviors (e.g., "git diff naturally shows all files") do not count as PASS — the instruction must explicitly state the required behavior.

---

*Optimized by AutoResearch Pipeline v2.0 — [presientlabs.com](https://presientlabs.com)*