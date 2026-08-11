# korkmaz

A [Cline](https://cline.bot) rule set. It gives a project two opposed
objectives: delivery speed and reliability. An error budget decides between
them.

The design comes from Korkmaz & Aydin (2025). That study compared four DevOps
team formations. The formation with two separate teams and continuous
collaboration performed best.

**This project is not affiliated with the authors. The authors did not review
it or approve it.**

## Install

1. Copy the `.clinerules` directory into the root directory of your project.
2. Replace each `{{FILL: ...}}` marker with a value for your project.
3. Enable subagents in Cline. Go to **Settings > Features > Agent**.
4. Run `/prr` on your next change.

To find the markers:

```bash
grep -rn "{{FILL" .clinerules
```

## Adapt with an agent

Give this instruction to the agent in your project:

> Clone `https://github.com/adiwastu/korkmaz`. Copy its `.clinerules`
> directory into this project. Find each `{{FILL: ...}}` marker. Replace each
> marker with the correct value for this project. Delete the sections that do
> not apply. Then delete the cloned repository.

## Fields to adjust

| File | Field |
| --- | --- |
| `20-reliability.md` | The services, SLIs, and SLO targets |
| `20-reliability.md` | The runbook links |
| `30-arbitration.md` | The command that prints the remaining error budget |

If you do not supply the error budget command, the rules use the Caution
posture for all changes. This is safe, but it is slow.

## Files

| File | Function |
| --- | --- |
| `.clinerules/10-delivery.md` | Sets the delivery objective. Metrics: deployment frequency, lead time. |
| `.clinerules/20-reliability.md` | Sets the reliability objective. Metrics: incidents, failures, MTTR. |
| `.clinerules/30-arbitration.md` | Decides between the two objectives. Uses the error budget. |
| `.clinerules/workflows/prr.md` | `/prr` starts a production readiness review. |
| `.clinerules/workflows/incident.md` | `/incident` starts incident response. |

Do not delete `30-arbitration.md`. Without it, the two objectives merge. The
rule set then has no effect.

## Limits

The study surveyed 105 people. The data is self-reported. It measures human
teams, not agents. Only one difference between the formations was
statistically significant.

## Source

Korkmaz HE and Aydin MN (2025) An empirical study on performance comparisons
of different types of DevOps team formations. *Front. Comput. Sci.* 7:1554299.
doi: [10.3389/fcomp.2025.1554299](https://doi.org/10.3389/fcomp.2025.1554299)

Open access under CC BY 4.0.

## License

MIT. See [LICENSE](LICENSE).
