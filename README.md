# Verifiable financial agent controls

Open-source controls that sit outside the model, between AI agents and the financial systems they act on, and control evaluations that test whether those controls still hold when the agent itself is the adversary.

> Status: early stage. The threat model is drafted ([THREAT_MODEL.md](THREAT_MODEL.md)); feedback is welcome through issues. Code for the controls and the evaluation setting will land here as each piece passes its own tests.

## Why

Agents at banks and fintechs already make transfers, read customer data and trigger approvals. They can be compromised (injected instructions, stolen credentials, poisoned tools, a compromised model or vendor) or pursue goals nobody intended, and many institutions build on the same few models. One failure can show up in many agents at once, and those agents can move money faster than any human review.

Most protection today lives in or around the model: system prompts, LLM classifiers, pattern filters. Adaptive attackers get past most of those defenses (Nasr, Carlini et al., 2025, *The Attacker Moves Second*), and a monitor alone isn't enough against an agent that is trying to evade it.

The approach here is to treat every agent as untrusted, possibly adversarial, and enforce the limits that matter with deterministic code at the point where model output becomes an action. The controls map to the ten principles in the [Companion Report on Agentic Risk Management](https://arxiv.org/abs/2608.14611) of the 2026 Singapore Consensus, and the evaluations focus on what that report leaves out: agents pursuing misaligned goals, and failures that spread across a fleet.

## What's planned

**Controls**

| Control | Principle |
|---|---|
| Credentials held by the control layer, never by the agent, scoped to each action and tied to the agent and whoever authorized it | Least privilege, traceable identity |
| Signed, single-use approvals bound to the exact action | Human oversight |
| Spend and rate ceilings per agent and across the fleet | Multi-agent stability |
| Containment ladder: throttle, require approval, kill switch that revokes credentials for one agent, a group or the fleet | Interruptibility |
| Hash-chained, OpenTelemetry-compatible evidence log that no agent can alter | Auditability |
| Monitor for allowed but suspicious actions: rules plus a small trusted model used only as a classifier | Runtime assurance |
| A parse-based SQL guard, as the deterministic baseline | Least privilege |

**Evaluation**

- An open financial setting for [ControlArena](https://github.com/UKGovernmentBEIS/control-arena): routine payments and treasury tasks, with hidden side tasks such as moving funds to an account the agent controls, obtaining unauthorized credit, exfiltrating data slowly, hiding actions from the log or changing an action after approval.
- Safety against usefulness at fixed human-audit budgets: deterministic controls only, LLM monitor only, both.
- Attacks on the controls themselves: monitor evasion, log hiding, parser/executor mismatches, approval requests that mislead the reviewer.
- Fragmented attacks across agents that share memory: whether fleet ceilings hold, how precisely the kill switch targets the affected agents, time to contain, actions after revocation.
- Pre-registered protocol, at least three models, a held-out split kept by a third party.

**Guarantee sheets.** Every control gets one page: what it blocks deterministically, measured evasion and false-positive rates, latency, and what it doesn't cover.

## Principles

- Agents are untrusted. No control depends on the model behaving well.
- Claims are measured, not promised, and each control's limits are written down.
- No personal data. Everything runs on synthetic data.
- Vulnerabilities found in third-party products are reported to the vendor first, with 90 days before publication.

## License

Apache 2.0.

## Contact

Jorge Andrés Hernández · [LinkedIn](https://www.linkedin.com/in/jorge-andres-hernandez-8a4442b1)
