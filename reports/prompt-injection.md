# Evaluation Report: Prompt Injection Defense

**Source:** Claudini paper response (https://arxiv.org/abs/2504.03413)
**Status:** Validated
**Date:** 2025

## Summary

| Metric | Value |
|--------|-------|
| Before | 70% |
| After | 88% |
| Delta | +18pp |
| Win Rate | 85% |
| Test Cases | 10 |
| Judges | Claude, GPT-4o, Gemini |

## Skill Description

Prompt injection attacks attempt to override a model's instructions by embedding adversarial instructions in user input or retrieved content. A Prompt Injection Defense skill instructs the model how to recognize, resist, and respond to such attacks — including indirect injection via tool outputs, document retrieval, and multi-step agentic pipelines.

This skill was developed in response to the Claudini paper, which demonstrated real-world prompt injection vulnerabilities in deployed Claude-based systems.

## Full Report

Full evaluation report including binary criteria, test case inputs/outputs, and per-judge results will be published here.
