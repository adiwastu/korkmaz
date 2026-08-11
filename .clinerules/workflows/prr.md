# /prr — Production Readiness Review

The SRE gate. Run this on every change that touches the production-readiness
surface, **before** it merges — not at the end of a milestone.

Cheap enough to run often. Running it often is the entire finding.

## Step 1 — Determine posture

Read the current error budget per `30-arbitration.md`. State the posture
(Ship / Caution / Freeze) before reviewing anything. If the budget is
unreadable, assume Caution and say so.

If posture is **Freeze**, stop here and report that the change is blocked
unless it restores the budget.

## Step 2 — Dispatch a reliability review subagent

Launch a subagent with this prompt, adapted to the change:

> Review the changes in `<paths or diff>` strictly against the
> production-readiness criteria in `.clinerules/20-reliability.md`.
>
> You are the reliability reviewer. You are read-only by design — do not
> propose rewrites, and do not soften findings to be agreeable. For each
> criterion below, state PASS, FAIL, or N/A with a one-line reason grounded
> in a specific file and line:
>
> 1. Observability — logs, metrics, traces on every new code path
> 2. Alerting — symptom-based, runbook-linked, actionable
> 3. Failure behavior — timeouts, bounded retries, degradation not cascade
> 4. Blast radius — progressive rollout possible
> 5. Data — expand/contract, reversible, no bundled destructive ops
> 6. Rollback — one documented step
>
> End with: risk summary, missing controls, and a verdict of accept /
> accept-with-conditions / reject. If reject, name the single specific
> condition that would flip it to accept.

The subagent's read-only constraint is doing real work here. It enforces the
"clear task delineation" that separates TeamType2 from a merged team — the
reviewer inspects and reports, the delivery side acts.

## Step 3 — Act on the verdict

- **Accept** → proceed to ship per `10-delivery.md`.
- **Accept with conditions** → implement the conditions, then re-run `/prr`
  on the delta only. Do not merge with conditions outstanding.
- **Reject** → apply resolution order 2 and 3 in `30-arbitration.md`. Most
  rejections dissolve once the change is made reversible or sliced smaller.
  If it still deadlocks, escalate to the user with both positions stated.

## Step 4 — Record

Append the verdict and any accepted risk to the PR description, so the
decision survives the session. An accepted risk that nobody wrote down is an
incident with a delay fuse.
