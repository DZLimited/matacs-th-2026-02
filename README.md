# AI Proposal Triage Design

## Assessment Information

- **Role:** Associate Software Developer
- **Reference:** MATACS-TH-2026-02
- **Candidate name:** M. Daniyal Zafar
- **Actual time spent:** 90 minutes

## Project Overview

This project proposes a human-supervised AI system for triaging proposals for a 150 kW rooftop solar installation at a warehouse in Windsor, Ontario.

The system uses AI to extract information from submitted proposals, deterministic code to validate mandatory requirements and calculate weighted scores, and human review to resolve uncertainty and make the final award decision.

The design aims to make proposal evaluation consistent, explainable, auditable, and resistant to instructions embedded in submitted documents.

## Repository Contents

- [System Design Write-up](WRITEUP.md) — answers to the five design questions.
- [AI Prompts](PROMPTS.md) — prompts for information extraction and evidence summaries.
- [Proposal Ranking](ranking.json) — proposed statuses and provisional ranking for proposals A–E.
- [System Flow Diagram](diagram/flow.png) — visual representation of the triage workflow.

## Key Design Decisions

- Mandatory requirements are checked separately from weighted scoring.
- Proposals with unresolved evidence are flagged for review rather than automatically rejected.
- Only eligible proposals proceed to scoring.
- Scoring criteria and weights are configurable.
- AI extracts proposal facts but does not independently decide eligibility, scores, rankings, or the final award.
- Human reviewers retain final award authority.
- Scores, supporting evidence, configuration versions, and reviewer overrides should be recorded for audit.
- Instructions embedded in submitted proposals are treated as untrusted content and must not influence evaluation rules.

## AI Use Disclosure

I used ChatGPT to help understand the assessment requirements, develop the system design, draft documentation, and refine the workflow diagram and proposed AI prompts.

**A place where I questioned or corrected an AI suggestion:** During the workflow design, I clarified that flagged proposals should not automatically proceed to scoring. A flagged proposal may have missing or unclear evidence, so it should return for human review and only proceed after the issue is resolved and eligibility is established. This helps prevent unsupported assumptions and unfair evaluation.

I reviewed the suggestions, refined the workflow, and am responsible for understanding and verifying the final submission.

## Limitations

This submission is a design exercise, not a deployed application. The ranking is provisional, and numeric scores have not been calculated. A production implementation would require a documented scoring rubric, validated evidence extraction, testing, and appropriate human oversight.
