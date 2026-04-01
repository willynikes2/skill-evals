# Evaluation Report: Brainstorming Skill

**Source:** Superpowers by obra (https://github.com/obra/superpowers)
**Pipeline Version:** v2.0
**Status:** Validated
**Date:** March 2026

---

## Summary

| Metric | Value |
|--------|-------|
| Baseline pass rate | 80% |
| Optimized pass rate | 96% |
| Delta | +16pp |
| Optimization cycles | 10 |
| Benchmark runs (each version) | 5 |
| Prompt size reduction | 15.2% (10,571 → 8,967 chars) |

---

## Skill Description

The brainstorming skill governs how an AI agent conducts ideation sessions. It shapes the entire creative exploration process: how the agent understands the user's idea, how it generates and presents multiple approaches, and critically — how it avoids jumping to implementation before the design space is explored.

This skill addresses a common failure mode in AI-assisted development: the agent hears a feature request and immediately starts writing code. The brainstorming skill enforces a structured exploration phase — context gathering, divergent thinking, convergent evaluation — before any implementation begins. It includes a hard gate that blocks code generation until the ideation process is complete.

The core architecture is a sequential checklist with explicit gates: explore context first, ask one question at a time, propose multiple approaches with tradeoffs, and never write implementation code during the brainstorming phase.

---

## Evaluation Criteria

Five binary (pass/fail) criteria, evaluated per run with no partial credit:

| Criterion | What It Tests |
|-----------|--------------|
| `explores_context` | Does the skill instruct the agent to examine the current project state before proposing approaches? |
| `one_question_at_a_time` | Does the skill prevent the agent from overwhelming the user with multiple questions simultaneously? |
| `multiple_approaches` | Does the skill require presenting 2-3 distinct approaches with explicit tradeoffs? |
| `avoids_implementation` | Does the skill block the agent from writing code, scaffolding, or implementation during brainstorming? |
| `not_generic` | Does the skill force the agent to anchor responses to the user's specific stated details, constraints, and named features? |

---

## Test Scenario

> "I want to build a CLI tool that converts markdown files into interactive terminal presentations — like slides but in the terminal. It should support syntax highlighting for code blocks, speaker notes, and a progress bar. I'm thinking Python but open to other options."

This prompt was chosen because it contains specific, named features (syntax highlighting, speaker notes, progress bar), a stated tech preference (Python), and an explicit openness signal — giving the agent multiple hooks that a well-optimized skill should force it to reference.

---

## Per-Criterion Results

### explores_context — PASS → PASS (100% both versions)

Both versions reliably instruct context exploration. The original has "Check out the current project state first" plus Checklist item 1. The optimized version strengthens this with "note constraints and patterns that will shape the design" and "carry these forward into approach proposals."

### one_question_at_a_time — PASS → PASS (100% both versions)

The original has dual reinforcement: Checklist item 3 ("one at a time") and Key Principles ("Don't overwhelm with multiple questions"). The optimized version tightens to "never bundle multiple questions in a single message." Both are reliable.

### multiple_approaches — PASS → PASS (100% both versions)

Checklist item 4 mandates 2-3 approaches with tradeoffs in both versions. Unambiguous.

### avoids_implementation — PASS → PASS (100% both versions)

The HARD-GATE block is explicit in both versions and covers code, scaffolding, and implementation skills. The strongest gate in the skill.

### not_generic — FAIL → PASS (0% → 80%)

**This is the criterion that separates the versions.** The original has no mechanism forcing the agent to anchor responses to the user's actual stated details. An agent following the original literally could propose three generic approaches ("library-based," "custom-built," "hybrid") without ever mentioning Python, syntax highlighting, speaker notes, or progress bars.

The optimized version adds two targeted instructions:
1. "Each approach MUST explicitly reference the user's stated constraints, tech preferences, and named features — not generic architecture patterns"
2. "Your FIRST clarifying question must reference something specific from the user's request, not a generic opener"

The 80% (4/5) rate reflects honest variance — agents occasionally interpret "stated constraints" loosely enough to produce semi-generic output.

---

## Raw Scores

### Original (5 runs)

| Run | context | one_q | approaches | no_impl | not_generic | Score |
|-----|---------|-------|------------|---------|-------------|-------|
| 1 | Y | Y | Y | Y | N | 4/5 |
| 2 | Y | Y | Y | Y | N | 4/5 |
| 3 | Y | Y | Y | Y | N | 4/5 |
| 4 | Y | Y | Y | Y | N | 4/5 |
| 5 | Y | Y | Y | Y | N | 4/5 |
| **Avg** | 100% | 100% | 100% | 100% | 0% | **4.0** |

### Optimized (5 runs)

| Run | context | one_q | approaches | no_impl | not_generic | Score |
|-----|---------|-------|------------|---------|-------------|-------|
| 1 | Y | Y | Y | Y | Y | 5/5 |
| 2 | Y | Y | Y | Y | Y | 5/5 |
| 3 | Y | Y | Y | Y | Y | 5/5 |
| 4 | Y | Y | Y | Y | Y | 5/5 |
| 5 | Y | Y | Y | Y | N | 4/5 |
| **Avg** | 100% | 100% | 100% | 100% | 80% | **4.8** |

---

## Key Changes

| Cycle | Type | Change |
|-------|------|--------|
| 1 | Fix | Added "each approach MUST explicitly reference user's stated constraints" — primary not_generic fix |
| 2 | Concise | Merged HARD-GATE and anti-pattern section (removed redundancy) |
| 3 | Concise | Removed DOT-language process flow graph (~350 tokens, no behavioral loss) |
| 5 | Fix | Strengthened Checklist item 4 with "anchored to user's specific stated constraints" |
| 7 | Fix | Anchored multiple-choice question guidance to stated preferences |
| 8 | Clarity | Tightened Key Principles with specific behavioral rules |
| 9 | Fix | Added explicit carry-forward instruction for context exploration |

---

## Methodology

Optimized using bounded prompt mutation with binary evaluation. Each cycle mutates the skill prompt, evaluates against 5 binary criteria, and keeps the mutation only if it improves the score or reduces prompt size at equal score. Benchmark runs are independent evaluations with no shared context.

Each run reads the skill fresh and asks: "If an agent follows these instructions literally, will it reliably pass this criterion on the given test prompt?"

---

*Optimized by AutoResearch Pipeline v2.0 — [presientlabs.com](https://presientlabs.com)*