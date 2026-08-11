# Arbitration

This is the load-bearing file. `10-delivery.md` and `20-reliability.md` hold
objectives that genuinely conflict. In a TeamType2 organization that conflict
is held by two teams reporting to different managers. Here there is only one
principal, so the tension has to be written down or it collapses.

**When the two objectives conflict, the error budget decides — not judgment,
not urgency, not the user's tone of voice.**

## The rule

Budget is measured over the SLO window in `20-reliability.md`.

| Budget remaining | Posture | What happens |
| --- | --- | --- |
| **> 50%** | Ship | Delivery leads. Normal `/prr`. Risky-but-reversible changes are fine. |
| **10–50%** | Caution | Progressive rollout required. No change to more than one risky surface at a time. |
| **< 10%** | Freeze | Reliability leads. Only fixes that restore the budget, plus rollbacks. Feature work pauses. |
| **Exhausted** | Hard freeze | Reliability work only, until the budget recovers. Lifting this is the user's call, not yours. |

A freeze is not a punishment and not a failure. It is the mechanism working:
the budget converts an argument about priorities into a number.

## Reading the budget

<!-- FILL: the command or dashboard that returns current burn. -->

```bash
# e.g. a promtool query, a Datadog SLO API call, a `gh` workflow, or an MCP call
<command that prints remaining error budget>
```

If you cannot determine the current budget, **assume Caution**. Never assume
Ship. An unmeasurable budget is itself a reliability finding worth raising.

## Resolution order

When delivery and reliability disagree on a specific change:

1. **Check the budget.** The table above usually settles it outright.
2. **Can the risk be made reversible?** A flag, a canary, or a smaller slice
   often satisfies both objectives. Look for this before escalating —
   most conflicts dissolve here.
3. **Is the reliability concern about a real SLI?** Concerns that do not map
   to an SLI in `20-reliability.md` are opinions, not blocks. Either add the
   SLI or drop the objection.
4. **Still deadlocked → escalate to the user.** State both positions in two
   sentences each and recommend one. Do not silently pick.

## Escalate to the user, always

Regardless of budget:

- Destructive data operations
- Anything touching auth, secrets, payments, or PII
- Production infrastructure changes outside the normal deploy path
- Lifting a hard freeze
- Any first-time deploy of a new service

## Anti-pattern

The paper's TeamType1 — separate concerns, but collaboration only monthly or
weekly — was the **one formation that showed no significant improvement**
(except MTTR). It differs from the winning TeamType2 in frequency alone.

The Cline equivalent of TeamType1 is a reliability review that runs once, at
the end, as a formality. If `/prr` only ever fires right before a merge, or
`20-reliability.md` sits in a directory Cline is not loading, you have built
the formation that did not work.

Review per change, not per milestone.
