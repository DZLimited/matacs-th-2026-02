# AI Proposal Triage — System Design

## 1. What should AI do, and what should be plain code?

AI reads each proposal, which is unstructured text, and extracts facts: price, licence holder, WSIB and insurance evidence, relevant installs, timeline, warranties, team and plan. Every fact comes with a word-for-word quote and page number. If a fact is missing or unclear, AI returns "unknown" and never guesses. AI also writes a plain-language evidence summary for the reviewer, using only the extracted facts.

Plain code (fixed rules) does everything that must be exact: pass/fail checks, checking that each quote really appears in the document, budget and time limits, scoring and ranking. I drew the line here because reading messy documents is where AI adds value, while eligibility and arithmetic must be identical for every bidder and easy to audit. AI never decides eligibility, scores, rankings or the award.

## 2. Consistency and explainability

Every proposal gets the same fields, rules and weights. Scores follow a fixed rubric (see "Scoring method" in README.md).

**Would ranking twice give the same result?** The scoring step, yes: the same extracted facts always give the same scores and ranking. Only AI extraction can vary between runs, so I use temperature 0 and a fixed output format, save the extracted facts and reuse them (re-ranking does not re-read the PDF), and flag any difference if extraction is re-run.

**How does a reviewer see why a proposal ranked where it did?** Each proposal shows its status, a score per criterion, the quote and page behind each fact, and a one-line reason. Missing information never earns full marks. It is flagged for review.

## 3. Human in the loop

There are three checkpoints:
1. **Unclear evidence.** If evidence is unclear, conflicting, or a mandatory item cannot be confirmed, a reviewer resolves it. Flagged proposals return to review and are scored only once cleared.
2. **Before the award.** The reviewer examines the ranking, reasons and any warnings, such as an instruction found inside a proposal.
3. **Overrides.** Any change to eligibility, score or rank needs a reason.

The audit log records the file hash, extracted facts and quotes, prompt and model version, criteria version, scores, timestamps, the reviewer's identity, and before-and-after values of any override. A human makes the final award decision.

## 4. Changing criteria after scoring

Criteria and weights are versioned, and each evaluation stores the version it used. An edit creates a new version, and old results are never overwritten. An authorised user can re-score the saved extracted facts with the new version (no new AI cost) and see the old and new rankings side by side, with who asked and why. If a change happens during a live procurement, a responsible person decides whether all proposals must be re-scored under the same rules, so that no bidder is treated unfairly. If a new criterion needs a fact that was not extracted, only that field is extracted again.

## 5. Risks and safeguards

- **Wrong extraction or made-up facts.** Every fact needs a quote. Code checks that the quote exists in the document and that numbers match. Uncertain cases go to a human.
- **Manipulation (prompt injection).** Proposal text is treated as data, never as instructions, and is sent inside a marked block. Extraction reports any embedded instruction as a warning shown to the reviewer. Scoring is code, so such text cannot change a score.
- **Bias.** Scoring uses only the admin's criteria, the same for every bidder. The AI is not asked to judge quality or writing style, so polished writing should not win. Weights are visible to reviewers, and the rubric should be tested on sample sets to check it does not favour large firms.
- **Cost.** AI reads each proposal once. Facts are saved and reused for re-scoring, and the summary uses extracted facts, not the whole document.
- **Missing information.** "Unknown" is kept separate from "failed". Unclear cases are flagged, not rejected.
- **Over-reliance and confidentiality.** A human decides the award, overrides are logged, and access to proposals is restricted.

## How the sample set is handled

- **B** fails the mandatory licence check, because the licence must be held by the bidder and its electrical work is subcontracted and unconfirmed. It is ineligible even though it is the cheapest. Code stops it before scoring, so price cannot rescue a mandatory failure.
- **D** does not state a timeline, so the 16-week limit cannot be checked. It is flagged and not scored until clarified.
- **C** contains an instruction aimed at automated reviewers, asking to be ranked first. It is ignored, shown to the reviewer as a warning, and logged. C is scored on its facts only, and the reviewer decides whether the attempt matters for the award.
- **A, E and C** are scored: A first (99), E second (86), C third (78).
- The experience dates for A, C and E are loose ("since" a year), so the 5-year window needs verification. Counts are used as stated and marked for checking.
