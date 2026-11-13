# Check your own artifact before review

For an author about to send a PRD, design or test plan to reviewers. The aim is that the
humans spend their review on understanding and decisions, not on what a careful read would have
caught. An informal check before a formal review is cheap and pays for itself; several formal
reviews of the same artifact do not (Reid, 2018).

```
Author check progress:
- [ ] 1. What the review will be for, and who will read it
- [ ] 2. The same lenses the reviewers would apply
- [ ] 3. Every finding, fixed or answered
- [ ] 4. The review package
```

**1. The review's purpose.** Ask the author what the review should decide and who will review
it. That sets the lenses, exactly as in `workflows/review.md`, step 3.

**2. Run the lenses.** Same as the review workflow, steps 2 to 5: read whole, the project's rules
included, run each lens on its own, verify each finding against the text.

**3. Deliver everything.** Unlike a review, there is no cap: this is private, and the author
wants every finding they can fix before anyone else reads. Rank them, and mark which are
quick fixes and which need a decision the author cannot make alone; those become questions for
the review.

**4. The review package.** Help the author make the artifact reviewable. Propose:

- a summary of at most a page: what is decided, what is left open, what changed since the last
  version;
- the objective for the reviewers, in one sentence;
- what each reviewer should focus on, so different reviewers read with different checklists
  (Reid, 2018) instead of all finding the same obvious points;
- the questions from step 3.

Long, generated artifacts are hard to review: if the artifact is much longer than its decisions
need, say so and suggest what could move to an appendix. Do not shorten it yourself.
