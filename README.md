# AI Cost Overrun Prevention & Budget Risk Advisor

An AI agent that predicts construction project cost overrun risk and recommends stage-specific mitigation strategies, built as a knowledge-grounded Claude Project.

## Overview
This agent helps project managers and finance teams identify cost overrun risk early, before it hits the bottom line. It uses a weighted risk scoring framework, grounded in a structured construction-risk knowledge base, so every number and recommendation traces back to a documented source rather than being invented on the fly.

## Problem
- Cost overruns are a persistent margin risk in construction and engineering delivery
- By the time overruns are visible in the numbers, they're expensive to fix
- Most tooling is reactive, flagging variance after the fact instead of anticipating it
- Firms that deliver on budget win repeat work; the ones that don't lose margin and trust

## Solution
The agent assesses cost overrun risk across five weighted drivers:
- **Scope Creep**: uncontrolled change requests, weighted highest (35%)
- **Schedule Delays**: critical path slips, resource bottlenecks (30%)
- **Labor/Material Cost Inflation**: wage and commodity escalation (15%)
- **Rework & Quality Issues**: defects requiring redo (15%)
- **Design Complexity & Unknowns**: engineering surprises (5%)

## How It Works
1. **Input**: a project profile, including stage, budget, timeline, current issues, and resource status
2. **Processing**: scores each of the five factors 0–10 against documented thresholds, then applies a weighted formula: `(Scope×3.5) + (Schedule×3.0) + (Labor×1.5) + (Rework×1.5) + (Design×0.5)` → a 0–100 composite score
3. **Output**: a risk tier (LOW / MEDIUM / HIGH / CRITICAL), the overrun probability band, root-cause breakdown, and prioritized mitigations
4. **Grounding**: the agent is instructed to cite the knowledge base for every statistic and to ask clarifying questions rather than guess when project details are missing, so no invented numbers

## Risk Scoring Framework

| Risk Score (0-100) | Overrun Probability | Action |
|---|---|---|
| 0-21 | <5% | Continue as-is |
| 22-50 | 5-15% | Implement targeted mitigations |
| 51-72 | 15-30% | Escalate to PM/stakeholders; activate contingency |
| 73-100 | >30% | Full replan; consider project pause/restructure |

## Example Run

**Input**: Retail fit-out, €1.2M budget, 10-week timeline, week 4 (35% complete). 2 minor scope changes, schedule on track, a subcontractor quote 12% over budget due to a material shortage, and a 4% tiling defect rate across 3 wall sections.

**Agent output**:
- Risk score: 22.5 (Scope 7.0 + Schedule 0.0 + Labor 6.0 + Rework 9.0 + Design 0.5) → low end of MEDIUM RISK
- Root cause: rework is the largest single driver: a 4% defect rate maps to the KB's "repeated rework" tier, and it's cheaper to fix now (mid-project) than later
- It also cross-checked a secondary rule (Stage 2 red flags: rework >2% or cost variance >3%) and flagged that this reads as a sharper signal than the blended score alone suggests
- Recommended: daily QC sign-offs + root-cause the defect pattern this week; resolve the subcontractor overage before it carries into the next stage; retroactively put the two scope changes through formal change control
- It explicitly flagged one judgment call it made (scoring the cost-inflation factor above what the KB's duration-only rubric would default to, because a 12%-over quote is a documented red flag in its own right) rather than silently picking a number

## Build Notes
This was originally scoped as a Microsoft Copilot Studio project. The underlying pattern (custom instructions plus a grounded knowledge source plus no-code testing) is the same one Copilot Studio uses for agent building. It's implemented here as a Claude Project (Project instructions and Project knowledge) for practical reasons, but the architecture, and the discipline of source-grounded, cite-your-numbers reasoning, carries directly across either platform.

One real bug surfaced during testing and was fixed before calling this done: the original scoring formula's weights summed to 1.0 with each factor capped at 10, so the maximum possible score was 10, yet the risk bands went up to 70, making the HIGH and CRITICAL tiers mathematically unreachable. This was caught by hand-checking the model's arithmetic against a scenario it should not have been able to just recite, rather than trusting a plausible-looking answer. It was fixed by rescaling the weights to sum to 10 (a 0-100 score range) and rescaling the bands to match. See `ARCHITECTURE.md` for detail.

## Files
- `Construction_Cost_Overrun_Prevention_KB.txt`: knowledge base covering root causes, stage-based risk matrix, scoring model, mitigation playbook, and glossary
- `ARCHITECTURE.md`: how the knowledge base is structured, the grounding/instruction setup, and the scoring bug found during validation
- `README.md`: this file

---

**Built as a proof-of-concept for a Data & AI Analyst application, to demonstrate agent design, knowledge grounding, and predictive risk-modeling thinking applied to a construction-industry problem.**
