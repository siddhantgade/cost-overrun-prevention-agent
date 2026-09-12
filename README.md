# AI Cost Overrun Prevention & Budget Risk Advisor

An AI agent that predicts construction project cost overrun risk and recommends stage-specific mitigation strategies, built as a knowledge-grounded Claude Project.

## Why
Cost overruns are a persistent margin risk in construction and engineering delivery, and most tooling only flags variance after it's already happened. This agent is a predictive, not reactive, alternative: it scores overrun risk early enough to actually act on it.

## What It Does
Scores project risk across 5 weighted drivers, each grounded in a documented knowledge base:

- **Scope Creep** (35%): uncontrolled change requests
- **Schedule Delays** (30%): critical path slips, resource bottlenecks
- **Labor/Material Cost Inflation** (15%): wage and commodity escalation
- **Rework & Quality Issues** (15%): defects requiring redo
- **Design Complexity & Unknowns** (5%): engineering surprises

Input a project profile (stage, budget, timeline, current issues) and the agent returns a risk score, the top root causes, and prioritized mitigations, always citing the knowledge base rather than inventing numbers.

## Risk Scoring

| Score (0-100) | Overrun Probability | Action |
|---|---|---|
| 0-21 | <5% | Continue as-is |
| 22-50 | 5-15% | Targeted mitigations |
| 51-72 | 15-30% | Escalate; activate contingency |
| 73-100 | >30% | Full replan |

## Example

**Input**: Retail fit-out project, week 4 of 10 (35% complete). 2 minor scope changes, on schedule, a subcontractor quote 12% over budget, and a 4% tiling defect rate.

**Output**: Risk score 22.5 (Medium). Primary driver: rework, not the more visible cost overage. Recommended actions: root-cause the defect pattern this week, resolve the subcontractor issue before the next project stage, retroactively formalize change control on the two scope changes.

## Implementation
Built as a Claude Project (custom instructions + Project knowledge), the same pattern Copilot Studio uses for agent building (grounded knowledge source + instructions + no-code testing). Architecture is portable across either platform.

A scoring formula defect was found and fixed during validation. See `ARCHITECTURE.md` for details.
