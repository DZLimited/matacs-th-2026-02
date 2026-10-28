# AI Proposal Triage Design

## Assessment information

- **Role:** Associate Software Developer
- **Reference:** MATACS-TH-2026-02
- **Candidate:** Muhammad Daniyal Zafar
- **Actual time spent:** About 90 minutes for the first version, plus about 20 minutes revising it after review.

## Project overview

This project designs a human-supervised, AI-assisted tool that triages contractor proposals. A reviewer uploads the proposals and selects a category. AI extracts facts from each proposal, fixed rules check the mandatory requirements and calculate weighted scores using criteria set by an admin, and the tool returns a ranked list with a reason for each position. A human makes the final award decision.

## Repository contents

- [WRITEUP.md](WRITEUP.md): answers to the five design questions, and how the sample set is handled.
- [PROMPTS.md](PROMPTS.md): prompts for information extraction and evidence summaries.
- [ranking.json](ranking.json): status, rank, score and reason for proposals A–E.
- [diagram/flow.png](diagram/flow.png): process flow from upload to ranked output.

## Result for the sample set

A is ranked first, E second and C third. D is flagged because its timeline cannot be checked. B is ineligible because it fails a mandatory requirement.

## Key design decisions

- Mandatory requirements are checked separately from weighted scoring.
- Proposals with unresolved evidence are flagged for review, not automatically rejected.
- Only eligible proposals proceed to scoring. Flagged proposals are scored only after a reviewer clears them.
- Criteria and weights are set by an admin, versioned, and never hard-coded.
- AI extracts facts and quotes. It does not decide eligibility, scores, rankings or the award.
- Code checks that every quoted source actually appears in the document.
- Instructions found inside proposals are treated as untrusted content, shown to the reviewer as a warning, and never affect the rules.
- Scores, evidence, configuration versions and reviewer overrides are recorded for audit.
- A human reviewer makes the final award decision.

## Scoring method (example rubric)

Mandatory criteria are pass/fail and are checked first. Only proposals that pass are scored.
Each weighted criterion is converted to a 0–100 score, multiplied by its admin-set weight, and added up.
This is an example configuration: an administrator can change the weights or rules without changing the code.

| Criterion | Weight | How it is scored (0–100) |
|---|---|---|
| Relevant experience | 30% | Installs ÷ highest install count among eligible proposals |
| Price | 25% | Lowest eligible price ÷ this proposal's price |
| Timeline | 15% | Shortest weeks ÷ this proposal's weeks |
| Warranty | 15% | Half from panel years ÷ longest, half from workmanship years ÷ longest |
| Team and approach | 15% | 25 points each for: named lead, crew, safety plan, monitoring plan |

Results for the sample set:

| Proposal | Experience | Price | Timeline | Warranty | Team | Total |
|---|---|---|---|---|---|---|
| A | 100 | 100 | 100 | 91.7 | 100 | 98.8 |
| E | 57.1 | 98.1 | 92.3 | 100 | 100 | 85.5 |
| C | 64.3 | 97.5 | 85.7 | 91.7 | 50 | 77.8 |

Notes:
- Scores are relative to the other eligible proposals, so they are recalculated when an eligible proposal is added or a flagged one (such as D) is cleared.
- Figures are used as stated in each proposal. Experience dates are marked for verification.
- A monitoring plan means monitoring the installed system's performance. Progress reports alone do not count.

## AI use disclosure

I used ChatGPT to understand the brief and draft the first version of the design, documents and prompts, and Claude to review the repository against the brief, which led to the corrected ranking.json format, the scoring rubric and a fuller write-up. I corrected the AI's workflow so that flagged proposals return to human review and are scored only once cleared, because scoring unclear evidence would rest on unsupported assumptions. I reviewed every change and can explain the final design.

## Limitations

This is a design exercise, not a built application. The scoring rubric is an example that an administrator can change. Extraction accuracy and the prompts have not been tested on real documents. Scores are relative to the other eligible proposals and must be recalculated when the eligible set changes.
