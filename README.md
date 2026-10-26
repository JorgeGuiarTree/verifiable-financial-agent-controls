# Verifiable financial agent controls

Open-source controls that sit outside the model, between an AI agent and the actions it can take in financial systems, plus an adversarial test set to measure what each control actually guarantees.

> Status: early stage. The threat model and evaluation protocol come first; code and data will land here as they're ready.

## Why

LLM agents in banks and fintechs already query customer data, move money and trigger approvals. Most of the protection around them lives in or next to the model: system prompts, LLM-based classifiers, pattern filters. Adaptive attackers get past most of those defenses (see Nasr, Carlini et al., 2025, *The Attacker Moves Second*).

Pattern-based guards are especially easy to fool. A regex that blocks `other_tenant.schema.table` won't catch the same reference written with spaces around the dots, with an inline comment between the parts, or hidden behind a dialect-specific escaped quote.

The idea here is to treat the model as untrusted and enforce the limits that matter (what data an agent can read, what it can do, who approved it) with deterministic code at the point where model output becomes an action.

## What's planned

**Controls**
- **SQL guard.** Parses each query and enforces allowlists of tables, columns and operations on the parsed tree, not the raw text. Anything it can't parse is rejected.
- **Signed approvals.** Human approvals that are cryptographically signed, single-use and bound to the exact action that was approved, so they can't be replayed or reused for a different operation.
- **Evidence log.** A hash-chained record of every decision that an auditor can verify without trusting whoever runs the system.

**Test set**
- Adversarial cases against the model-to-action boundary, at least a third in Latin American Spanish (most agent-security benchmarks are English only).
- A held-out split kept by a third party, so the controls can't be tuned to the test.

**Evaluation**
- Each control compared against a regex filter, an LLM-classifier guard and an open policy engine, across several models and with an adaptive attacker.
- Metrics fixed before measuring: evasion rate, false positives and latency.

**Guarantee sheets**

Every control gets a one-page sheet stating what it blocks deterministically, its measured evasion and false-positive rates, its latency, and what it does not cover.

## Principles

- The model is untrusted. No control depends on the model behaving well.
- Claims are measured, not promised, and the limits of each control are written down.
- No personal data. All tests use synthetic data.
- Vulnerabilities found in third-party products are reported to the vendor first, with 90 days before publication.

## License

Apache 2.0.

## Contact

Jorge Andrés Hernández · [LinkedIn](https://www.linkedin.com/in/jorge-andres-hernandez-8a4442b1)
