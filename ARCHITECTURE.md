# System Architecture & Design

## Knowledge Base Structure
The KB (`Construction_Cost_Overrun_Prevention_KB.txt`) is organized into 6 sections:
1. **Root Causes & Early Indicators**: why overruns happen across five drivers (scope, schedule, labor/material inflation, rework, design complexity), with historical cost ranges for each
2. **Budget Risk Assessment Matrix**: risk indicators and mitigations mapped to five project stages, from pre-contract planning through final handover
3. **Risk Scoring Model**: the weighted formula and per-factor scoring tiers (0-10 scale per factor)
4. **Mitigation Playbook**: specific actions and expected impact for each risk driver
5. **Real Project Scenarios**: three worked examples used to sanity-check the agent's arithmetic during testing
6. **Glossary**: key terms (critical path, contingency, change order, etc.)

## Agent Configuration
- **Project instructions**: define the agent's role (assess risk, identify root causes, recommend mitigations, answer operational questions), enforce grounding ("do not invent statistics: every cited number must come from the knowledge base"), require clarifying questions when project details are missing, and specify a fixed 4-part output structure (Risk Assessment / Root Causes / Recommended Actions / Next Steps)
- **Project knowledge**: the KB file above, used as the agent's sole source of scoring thresholds, mitigation actions, and historical figures

## The Scoring Bug (and why it mattered)
The first draft of the formula used weights that summed to 1.0 (0.35/0.30/0.15/0.15/0.05) against factors scored 0-10, so the true maximum score was 10. The risk-tier bands, however, were written to run 0-70 (LOW 0-15 up to CRITICAL 51-70), meaning the HIGH and CRITICAL tiers could never mathematically be reached. The worked example in the KB ("score = 37, HIGH RISK") did not actually follow from the stated formula.

This was caught by hand-recomputing a scenario's expected score independently and comparing it against what the agent reported, rather than treating a plausible-looking answer as correct. The first live test the agent ran happened to reuse a scenario copied almost verbatim from the KB, and the agent returned the KB's own (incorrect) worked answer. That's consistent with retrieval rather than calculation, so it wasn't proof the formula worked.

**Fix**: rescaled the weights to 3.5/3.0/1.5/1.5/0.5, which sum to 10, giving a real 0-100 range, and rescaled the interpretation bands proportionally (0-21, 22-50, 51-72, 73-100). All three worked scenarios in the KB were recalculated and corrected to match.

**Validation**: re-tested with a scenario built from scratch (not present anywhere in the KB). The agent showed its per-factor arithmetic before totaling, correctly identified the project stage from % complete rather than elapsed weeks, cross-checked its blended score against a separate stage-specific red-flag rule, and explicitly flagged a judgment call it made where the KB's rubric was ambiguous (scoring cost-inflation from a supplier quote red flag rather than the rubric's duration-only default) instead of silently picking a number.

## Why This Matters
Grounding an agent in a knowledge base doesn't guarantee correctness: it only guarantees the agent draws from that source. If the source itself contains an internal inconsistency, a grounded agent will faithfully reproduce it. The only way to catch that is to independently verify the agent's output against ground truth on a case it hasn't seen before, not just check that it cites the knowledge base. That verification step is what turned this from "an agent that sounds confident" into one whose numbers actually hold up.
