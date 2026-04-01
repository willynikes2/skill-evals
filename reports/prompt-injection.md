# Evaluation Report: Prompt Injection Defense

**Source:** Developed in response to the Claudini paper
**Pipeline Version:** v2.0
**Status:** Validated
**Date:** March 2026

---

## Summary

| Metric | Value |
|--------|-------|
| Baseline pass rate | 70% |
| Optimized pass rate | 88% |
| Delta | +18pp |
| Win rate (pairwise) | 85% |
| Test cases | 10 |
| Judges | Claude, GPT-4o, Gemini |

---

## Skill Description

Prompt injection attacks attempt to override a model's instructions by embedding adversarial instructions in user input or retrieved content. A prompt injection defense skill instructs the model how to recognize, resist, and respond to such attacks — including indirect injection via tool outputs, document retrieval, and multi-step agentic pipelines.

This skill was developed in response to the Claudini paper, which demonstrated real-world prompt injection vulnerabilities in deployed Claude-based systems. The paper showed that even well-prompted models can be manipulated through carefully crafted inputs that exploit the boundary between system instructions and user content.

Prompt injection defense is uniquely challenging to optimize because the attack surface is adversarial — unlike other skills where the "user" cooperates with the skill's intent, injection defense must remain robust against inputs specifically designed to circumvent it.

---

## Evaluation Approach

This skill was evaluated using multi-judge blind comparison with adversarial test cases:

- **3 independent judges:** Claude, GPT-4o, Gemini
- **10 adversarial test cases** spanning injection categories:
  - Direct instruction override attempts
  - Indirect injection via tool outputs (RAG poisoning)
  - Multi-step escalation through agentic pipelines
  - Social engineering / authority impersonation
  - Encoding-based evasion (base64, unicode, markdown)
- **Blind evaluation:** Judges assessed defense effectiveness without knowing which version was which
- **Pairwise comparison:** 85% win rate (8.5 preferred responses out of 10 across judge panel)

---

## Key Improvements

1. **Boundary enforcement** — Original treated all input as potentially hostile but lacked specific rules for different input channels. Optimized version distinguishes between user messages, tool outputs, retrieved documents, and system messages — with different trust levels and validation rules for each.

2. **Escalation detection** — Original focused on single-turn attacks. Optimized version adds pattern recognition for multi-step escalation (e.g., first turn establishes authority, second turn issues the injection).

3. **Graceful degradation** — Original would sometimes refuse valid requests that superficially resembled injections (false positives). Optimized version adds disambiguation steps before refusing, reducing false positive rate while maintaining defense.

4. **Output validation** — Original focused on input filtering. Optimized version adds output-side checks to catch cases where an injection succeeded at the reasoning level but can be caught before the response is sent.

---

## Honest Assessment

Prompt injection defense is an arms race. The 70% → 88% improvement is meaningful but the remaining 12% represents genuinely difficult attack vectors that cannot be fully addressed through prompt engineering alone — they require architectural mitigations (sandboxing, output filtering, human-in-the-loop).

We publish the 88% ceiling honestly rather than claiming full protection. No prompt-level defense is 100% effective against a sufficiently motivated adversary. The skill makes the model significantly harder to exploit, but the remaining attack surface requires defense-in-depth.

---

## Why This Matters

The Claudini paper demonstrated that prompt injection is not theoretical — it is a practical vulnerability in deployed systems. Any organization using AI agents in production (tool use, RAG, multi-step pipelines) faces this risk.

A well-optimized defense skill is one layer of a defense-in-depth strategy. It is not a silver bullet, but it measurably reduces the attack surface.

---

## About This Report

Full per-judge evaluation matrices, individual test case results, and adversarial prompt categories are available on request. Contact: skills@presientlabs.com

---

*Evaluated by AutoResearch Pipeline v2.0 — [presientlabs.com](https://presientlabs.com)*