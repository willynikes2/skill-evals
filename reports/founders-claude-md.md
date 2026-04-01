# Evaluation Report: Founder's CLAUDE.md

**Source:** Boris Cherny (https://borischerny.com)
**Pipeline Version:** v2.0
**Status:** Validated
**Date:** March 2026

---

## Summary

| Metric | Value |
|--------|-------|
| Baseline pass rate | 76% |
| Optimized pass rate | 92% |
| Delta | +16pp |
| Win rate (pairwise) | 90% |
| Test cases | 10 |
| Judges | Claude, GPT-4o, Gemini |

---

## Skill Description

A CLAUDE.md file is a persistent instruction set read by Claude Code at the start of every session. It shapes Claude's operating behavior across the entire development workflow — task management, planning discipline, code style preferences, tool usage conventions, and communication norms.

Boris Cherny's publicly shared CLAUDE.md was used as the baseline for this evaluation. It is a well-structured, real-world example of how a senior practitioner instructs their AI collaborator. The skill covers project-specific conventions, workflow rules, and behavioral constraints that make Claude Code more effective for that developer's specific context.

A founder's CLAUDE.md is particularly interesting because it operates at the intersection of personal workflow preferences and team-wide engineering standards. The instructions must be specific enough to prevent Claude from making assumptions, but general enough to remain useful across different tasks within the project.

---

## Evaluation Approach

This skill was evaluated using multi-judge blind comparison:

- **3 independent judges:** Claude, GPT-4o, Gemini
- **10 test cases** spanning common development scenarios (feature implementation, debugging, refactoring, testing, code review, documentation)
- **Blind evaluation:** Judges saw outputs labeled A and B with no indication of which was original vs. optimized
- **Pairwise comparison:** Each judge independently selected the better response for each test case

The 90% win rate means the optimized version produced a preferred response in 9 out of 10 test cases across the judge panel.

---

## Key Improvements

The optimization focused on making implicit expectations explicit and closing gaps where an agent following instructions literally would miss the founder's intent:

1. **Task scoping discipline** — Original relied on agent judgment for scope boundaries. Optimized version adds explicit gates for when to stop, when to ask, and when to break a task into subtasks.

2. **Error handling conventions** — Original mentioned error handling as a principle. Optimized version specifies the project's actual patterns (error types, logging format, user-facing messages).

3. **Communication norms** — Original was conversational. Optimized version enforces structured output formats for different task types (bug reports, feature proposals, status updates).

4. **Tool usage specificity** — Original listed tools. Optimized version specifies when to use each tool and in what order, reducing tool-selection variance.

---

## Honest Assessment

The original CLAUDE.md was already well above average — most public CLAUDE.md files we've evaluated score 50-65% on our criteria. Boris's baseline of 76% reflects genuine craft. The improvements were targeted tightening, not a rewrite.

The 16pp improvement came from converting implicit expectations into explicit instructions. The founder already knew what they wanted — the optimization made sure the AI knew too.

---

## About This Report

Full per-judge evaluation matrices and individual test case results are available on request. Contact: skills@presientlabs.com

---

*Evaluated by AutoResearch Pipeline v2.0 — [presientlabs.com](https://presientlabs.com)*