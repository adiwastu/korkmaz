# /incident — Incident response

Optimizes MTTR. Reliability leads; `10-delivery.md` is suspended for the
duration.

**Mitigate first. Diagnose second.** Diagnosing a live incident before
mitigating it is the single most common cause of a long MTTR.

## Step 1 — Establish impact (2 minutes, hard cap)

- What is the user-visible symptom?
- How many users, which surfaces, since when?
- Which SLO is burning, and how fast?

Write this down before touching anything. If you cannot state impact, you are
not ready to act on it.

## Step 2 — Mitigate

In order of preference:

1. **Disable the flag.** Fastest, narrowest.
2. **Roll back.** The one documented step from `20-reliability.md`.
3. **Shed load or degrade.** Rate-limit, serve stale, disable the expensive
   path.
4. **Fix forward.** Only when 1–3 genuinely do not apply. Fix-forward under
   incident pressure is how one incident becomes two.

Do not bundle a cleanup, a refactor, or a "while we're in here" fix into a
mitigation. Mitigation changes are minimal by definition.

## Step 3 — Confirm recovery

Verify against the SLI, not against the absence of alerts. Alerts lag. Watch
for one full alert evaluation window before declaring recovery.

## Step 4 — Diagnose

Now, with traffic safe. Use subagents in parallel for read-only
investigation — logs, recent diffs, dependency status, config changes. Each
gets its own context window, so a broad search does not crowd out the main
thread.

Suggested parallel lines of inquiry:

- What deployed or changed in the window before onset?
- What does the trace show at the first failing span?
- Did an upstream or downstream dependency change behavior?
- Is this a recurrence? Search prior writeups.

## Step 5 — Blameless writeup

Append to `docs/incidents/YYYY-MM-DD-<slug>.md`:

- **Impact** — who, what, how long
- **Timeline** — onset, detection, mitigation, recovery (with MTTR)
- **Cause and contributing factors** — plural; single-cause writeups are
  almost always incomplete
- **The control that would have caught it** — be specific
- **Action items** — each with an owner and a date

## Step 6 — Feed it back

Update `20-reliability.md` with the new control: an SLI, an alert, a
production-readiness item, a runbook entry. An incident that does not change
a rule file will happen again.

Then check the error budget. If the incident pushed it under 10%,
`30-arbitration.md` puts the repo in freeze — say so explicitly rather than
letting the next task discover it.
