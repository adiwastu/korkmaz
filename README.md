# korkmaz

A [Cline](https://cline.bot) rule set that ports the best-performing DevOps
team formation from empirical research into an agent configuration.

Named for Halil Ergun Korkmaz, lead author of the paper this is built on.
*Korkmaz* also means "does not fear" in Turkish — which is roughly what an
error budget buys you.

> **Not affiliated with, endorsed by, or reviewed by the paper's authors.**
> This is an independent interpretation of their published findings.

---

## The finding

Korkmaz & Aydin surveyed 105 DevOps professionals across four team formations,
measuring five metrics before and after DevOps adoption.

| | Formation | n |
| --- | --- | --- |
| **TeamType1** | Separate Dev/Ops, **limited** collaboration | 9 |
| **TeamType2** | Separate Dev/Ops, **high** collaboration (Google SRE model) | 27 |
| **TeamType3** | Separate Dev/Ops + a platform/infra team | 40 |
| **TeamType4** | Single merged team — "you build it, you run it" | 28 |

Standardized effect sizes (Cohen's *d*, pre → post adoption; more negative is
better):

| Metric | TeamType1 | TeamType2 | TeamType3 | TeamType4 |
| --- | --- | --- | --- | --- |
| Lead Time | −0.54 | −0.60 | **−1.05** | −1.00 |
| Deployment Frequency | −0.51 | −1.03 | **−1.20** | −0.69 |
| MTTR | −0.90 | −0.93 | −0.85 | **−1.23** |
| Number of Incidents | −0.08 | **−1.18** | −0.87 | −0.64 |
| Failures / Interruptions | −0.26 | **−1.46** | −0.94 | −0.74 |

**TeamType2 wins**, best in three of five metrics. Its failure-rate effect
(−1.46) is the largest in the study. TeamType1 is the only formation that
essentially failed — no significant improvement except MTTR, and a negligible
effect on incidents.

The counterintuitive part: TeamType1 and TeamType2 have the *same* structure.
They differ only in **collaboration frequency**. Continuous beats monthly by
enough to flip a formation from "didn't work" to "best in class."

## The port

TeamType2 is two teams with **clear task delineation**, **continuous
collaboration**, and — critically — **different objectives**, often reporting
to different managers.

That last property is what separates it from the merged TeamType4. Dev
optimizes throughput; Ops optimizes reliability; neither can unilaterally
win. With a single agent there is no second principal, so the tension has to
be written down or the config silently degrades into TeamType4.

```
.clinerules/
├── 10-delivery.md      # throughput mandate — DF, LT
├── 20-reliability.md   # reliability mandate — NoI, NoF/NoSI, MTTR
├── 30-arbitration.md   # error budget — resolves the conflict
└── workflows/
    ├── prr.md          # /prr — production readiness review
    └── incident.md     # /incident — MTTR path
```

`30-arbitration.md` is load-bearing. Delete it and you have TeamType4 with
extra reading.

**Why this fits Cline specifically.** Cline subagents are
[read-only by design](https://docs.cline.bot/features/subagents) — they can
read, search, and run read-only commands, but cannot edit files, use MCP
servers, or spawn nested subagents. An advisory reviewer that inspects
everything and changes nothing *is* the SRE posture. The constraint that
looks like a limitation turns out to enforce exactly the delineation
TeamType2 depends on.

## Setup

1. Copy `.clinerules/` into your repo root. Cline picks it up automatically
   in the CLI, VS Code extension, and JetBrains plugin.

2. Replace every `<!-- FILL -->` placeholder:

   | File | What to fill |
   | --- | --- |
   | `20-reliability.md` | Your real SLOs and SLIs |
   | `20-reliability.md` | Runbook links (or delete the table) |
   | `30-arbitration.md` | The command that reads current error budget burn |

3. Confirm subagents are on: **Settings → Features → Agent**.

4. Run `/prr` on your next non-trivial change.

If you skip step 2, the rules still load but arbitration cannot read a budget
— it will default to Caution posture on everything, which is safe but slow.

## Adapting it

**Have a real platform team?** Consider porting **TeamType3** instead. A
platform team providing "highly automated infrastructure services" maps
almost directly onto a well-built MCP server set plus shared workflows, and
TeamType3 won on Lead Time with the largest Deployment Frequency effect in
the table.

**Solo on a small project?** TeamType4's merged posture is less overhead and
it won MTTR outright. Keep `20-reliability.md` and `workflows/`, drop the
two-objective split.

## Caveats

Read these before treating any of it as established.

**The evidence doesn't transfer.** This is self-reported before/after recall
on 5-point scales from 105 humans about human organizations adopting DevOps.
It says nothing about agent configurations. What ports is a structural
argument, not a validated result.

**The paper's own ranking is soft.** The body's "best performer" calls use raw
mean differences, but the Cohen's *d* table ranks Deployment Frequency as
TeamType3 (−1.20) above TeamType2 (−1.03). Those disagree.

**Only one between-type difference reached significance** in the entire study
— Deployment Frequency goal achievement, TeamType2 (4.19) over TeamType4
(3.39). Post-adoption, the formations were statistically indistinguishable on
Lead Time, MTTR, and failure rate.

**TeamType1's n is 9**, against 27/40/28 for the others. Its "failure" is at
least partly an underpowered cell.

## Citation

> Korkmaz HE and Aydin MN (2025) An empirical study on performance
> comparisons of different types of DevOps team formations.
> *Front. Comput. Sci.* 7:1554299. doi:
> [10.3389/fcomp.2025.1554299](https://doi.org/10.3389/fcomp.2025.1554299)

Open access under CC BY 4.0. The paper is not vendored here — read it at the
DOI above.

Related work the paper builds on: Lopez-Fernandez et al. (2021), Leite et al.
(2021, 2023) on DevOps team structure taxonomies, and Beyer et al. (2016) for
the SRE model TeamType2 is drawn from.

## License

MIT — see [LICENSE](LICENSE). The rule text is mine; the research findings
are the authors' and are cited above.
