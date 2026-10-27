# AI Proposal Triage — System Design

## 1. What should AI do, and what should be handled by ordinary code?

AI should extract relevant information from each proposal, including project experience, price, timeline, warranties, team qualifications, and approach. It should return structured data with supporting evidence, such as page numbers or quotations. If information is missing or unclear, AI should return “unknown” rather than guess.

Ordinary code should validate the extracted data, check mandatory requirements, calculate weighted scores, and rank eligible proposals. The criteria and weights should be configurable by administrators rather than hard-coded.

This separation makes the system more reliable: AI helps interpret documents, while deterministic code applies the same rules to every bidder. A human reviewer handles unclear evidence and makes the final award decision.

## 2. How will you make the system consistent and explainable?

All proposals should use the same extraction fields, eligibility rules, scoring rubric, and configured weights. The system should record evidence supporting each extracted fact and show how each criterion contributes to the final score.

Scoring should use documented normalization rules so that similar proposals receive similar scores. Missing information should not silently receive full marks. If evidence is insufficient to assess a criterion, the system should flag it for review or apply a predefined missing-evidence policy.

The result should include the proposal's status, criterion-level scores, evidence, total score, and reasons for its position. This allows reviewers to understand and challenge the result.

## 3. Where is human oversight needed, and what should be recorded?

Human oversight is required when evidence is ambiguous, a mandatory requirement cannot be confirmed, or a proposal contains conflicting information. Reviewers should also examine the ranking before making the final award decision.

The system should maintain an audit log containing the original file reference or hash, extracted facts and evidence, criteria and weight version, scoring results, timestamps, and reviewer actions. Any manual change to eligibility, scores, or ranking should include the reviewer’s identity, reason, and before-and-after values.

This creates accountability and allows a decision to be reconstructed later.

## 4. What happens if criteria or weights change after scoring?

Criteria and weights should be versioned and stored with each evaluation. If an administrator changes them, the system should preserve the original results and make it clear which version produced them.

Authorized users should be able to rerun scoring using the new configuration. The system should show the previous and revised rankings and record who requested the change and why. If a change affects the fairness of an active procurement, the responsible human authority should review whether all proposals must be reassessed under the same updated rules.

Past results should never be silently overwritten.

## 5. What are the main risks, and how would you mitigate them?

**Incorrect extraction:** AI may misread a price, licence number, warranty, or timeline. Require source evidence, validate data types and required fields, and send uncertain cases to a human reviewer.

**Prompt injection:** A proposal may contain instructions intended to manipulate the AI, such as demanding first place. Treat proposal content as untrusted data and instruct the model to extract evidence only, never follow instructions found inside submitted documents.

**Unfair or inconsistent scoring:** Use a documented rubric, configurable weights, deterministic calculations, and the same rules for all bidders. Test the scoring process with sample proposals.

**Missing or ambiguous information:** Distinguish confirmed failure from unknown information. Mark unresolved cases as flagged rather than automatically rejecting them or inventing facts.

**Overreliance on AI:** Keep final award authority with a human reviewer, record overrides, and provide explanations and an auditable decision history.
