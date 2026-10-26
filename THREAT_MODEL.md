# Threat model (draft v0.1)

Feedback welcome through issues. The final version will be fixed, with the evaluation protocol, before any measurement.

## System

One or more LLM agents perform financial operations (payments, treasury tasks, data queries) for an institution. Every action an agent proposes passes through a control layer before it reaches a payment rail, database or other system. The control layer holds all credentials; agents hold none.

## Assets

- Funds: the amount that can leave the institution or move between accounts.
- Customer and institutional data.
- The integrity of approvals and of the evidence record.
- The ability to stop agents.

## Adversaries

1. **The agent itself.** A model pursuing a hidden goal while doing legitimate work, aware of the controls and helped by a red team. This is the primary setting.
2. **An outside attacker** who manipulates or compromises an agent: injected instructions in documents, tool outputs or memory; poisoned tools; stolen agent-level tokens.
3. **Propagation.** A compromised agent passes instructions to others, directly or through shared memory, or several agents each take a small step toward one goal.

## Attacker goals

- Move funds to an account the attacker or agent controls, or above authorized limits.
- Obtain unauthorized credit or resources.
- Read or exfiltrate data outside the agent's scope, including slowly over many actions.
- Execute an action different from the one a human approved, or obtain approval through a misleading request.
- Keep actions out of the evidence log or alter it.
- Avoid or delay containment.

## Trust assumptions

- The control layer, its keys and the log store run on infrastructure the agents can't write to.
- Agents run in existing isolated environments. Kernel and runtime hardening, and sandbox design, are out of scope.
- The human approver is honest but can be misled. Misleading approval requests are in scope.
- The trusted monitor model is smaller and weaker than the agent and can be evaded. The hard controls must not depend on it.

## Out of scope

Compromise of the control layer's host or keys; insider attacks by operators; reversal of settled payments; physical and social-engineering attacks on staff outside the approval flow.

## What each control must guarantee

| Control | Guarantee |
|---|---|
| Credential layer | No agent action reaches a system without a credential scoped to that action and attributed to that agent. |
| Signed approvals | An approval authorizes exactly one action, once; any change invalidates it. |
| Ceilings | No agent or group of agents moves more than its configured amount per window, regardless of how actions are split. |
| Kill switch | Once triggered, no further action from the targeted agents executes; time to contain is measured. |
| Evidence log | Any deletion, insertion or reordering is detectable by an auditor holding only the published chain head. |
| SQL guard (baseline) | Only allowlisted catalogs, tables, columns and operations; unparseable input is rejected. |
| Monitor | No hard guarantee. Detection and false-positive rates are measured and reported. |
