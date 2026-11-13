# Review an artifact (or a set of them)

For a reviewer: someone else's PRD, design, test plan or PR, or several that must agree with
each other. Copy the checklist and check it off as you go:

```
Review progress:
- [ ] 1. Scope and objective agreed with the user
- [ ] 2. Everything in scope read whole, rules included
- [ ] 3. Lenses chosen, and the ones left out stated with a reason
- [ ] 4. Each lens run on its own
- [ ] 5. Findings verified against the text
- [ ] 6. Triaged: at most five blocking, notes grouped, questions written
- [ ] 7. Report delivered with its coverage
```

**1. Scope and objective.** Name each artifact in scope and its type (see
`references/artifacts.md`). Propose the objective from the request and ask the user to confirm
it with one line: "Objective: find drift between the PRD and the design before implementation
starts. Confirm, or tell me what this review is for." If the request names no related artifacts
but the objective needs them (traceability needs both ends), ask for them; do not infer one
side from the other.

**2. Read whole.** Read every artifact in full. Find and read the project's rules the lenses
will need: architecture overviews, ADRs, conventions, security or performance requirements,
the definition of done. Projects name and place these differently, so search by content, not
by a file name: the root and documentation folders, decision records, contribution guides,
instruction files written for coding agents, design templates, and rules enforced in
configuration (lint, dependency or CI rules). List what you found and where. If nothing turns
up, ask the user where the rules live before running a lens that needs them. An artifact
longer than you can read with attention is itself a finding about reviewability (see the
consistency lens); review the part the objective needs and say which part.

**3. Choose lenses.** Start from the defaults for each artifact type in `references/artifacts.md`,
then adjust by risk: money, personal data, safety, external dependencies and irreversible
operations each add a lens. Write the choice down:

| Lens | Run | Why |
|---|---|---|
| consistency | yes | default for a PRD |
| security | no | no personal data or authentication in scope |

**4. Run each lens.** For each chosen lens, load its file from `references/lenses/`, apply its
checklist to the artifact, and record findings in the format of `references/findings.md`. Keep
each lens's pass separate. With subagents available, run one per lens in parallel, each given
the lens file, the artifacts and the rules in full, and the objective, and nothing from this
conversation: a reviewer who has not heard the author's explanations reads only what the
artifact says, which is all its readers will have.

**5. Verify.** Re-read each finding against the text it quotes. Drop a finding when the quote
does not say what the finding claims, when another part of the artifact answers it, or when
it depends on a rule the project does not have. Keep a count of what you dropped; it goes in
the report.

**6. Triage.** Follow `references/findings.md`: merge duplicates across lenses, rank, pick at most
five blocking findings, group the rest as notes by lens, and write three to five questions for
the human review.

**7. Report.** Use the report template in `references/findings.md`: objective, coverage (lenses
run and not run, what could not be read), blocking findings, notes, questions. Lead with what
changes the decision the review exists for.

Do not edit the artifacts. If the user then asks you to apply fixes, apply only the ones they
name.
