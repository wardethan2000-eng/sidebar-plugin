# Sidebar plugin

Bundles the Sidebar case-law connector with eight skills for litigation work in Claude:

- **potential-claims**: which claims a client has or faces, from the facts: elements, limitations period and last day to file, remedies, defenses and viability per claim, with rejected claims kept.
- **motion-to-dismiss**: claim-by-claim, element-by-element analysis of a motion to dismiss from either side.
- **summary-judgment**: readiness, exposure, and a statement of facts or a response to one, checked against the record.
- **brief-teardown**: checks the other side's brief: citations, quotes, pins, good law, overstated holdings, later limiting cases, and omissions.
- **prefiling-check**: checks our own draft before filing: every citation, good law, later cases that limit our authorities, what the other side will cite, a red team as opposing counsel and as the judge, and record cites against the documents in the Project. Gives a fix list by severity; the next draft shows what was fixed and what is new.
- **recheck-matter**: run only when asked. Re-checks every authority the matter's files rely on: good law, new citing cases since the last recheck, amended statutes. Reports which files and conclusions are affected and offers updates; it never changes a finding on its own.
- **open-research**: open-ended research on a matter: lists every candidate theory (claims, defenses, other parties, procedure, remedies, fee-shifting), tries to kill each one cheaply, researches the survivors one subquestion at a time, and hands back a queue of next rabbit holes. Keeps a compounding `research/` folder: an index, a theory ledger, dispelled theories with what would revive them, and one memo per subquestion.
- **apply-new-facts**: applies a new or changed fact to the research already saved: finds every theory and memo that rests on it, re-evaluates each, revives dead theories whose conditions are met, and logs what moved.

Each skill keeps its work as a markdown file in the Claude Project (`claims/…`, `motions/…`, `briefs/…`, `rechecks/…`, `research/…`). It finds that
file on the next run, updates it, records what changed, and re-checks every citation before saving. The skills
ask for missing documents before they start. Standards, elements and limitations periods come from Sidebar's `legal_standard`,
`claim_elements` and `limitations_period` tools, and deadlines from `compute_deadline`, so they are not
written from memory.

## The matter files

All eight skills share two files at the root of the Project, and create them when they are missing:

- **`matter.md`**, the hub: court, case number, judge, parties and which side is ours, posture, the next deadlines,
  an index of every work file with a one-line status and date, the rechecks, and a dated change log.
- **`facts.md`**, the fact ledger: each fact with an id (F-001, F-002 …), its source (document and ¶, page or line),
  the date it was learned, and its status (alleged, admitted, disputed, proven, assumed, disproved, withdrawn). Work files cite facts by id.

Each skill reads both first, so it doesn't ask again for what they hold, and updates both when it finishes. The formats
are in `skills/<skill>/references/matter-files.md`. That file is the same in every skill folder: edit one copy, then
copy it to the others.

## Install

In Claude: **Plugins → Add → Add marketplace → Add from a repository**, enter
`wardethan2000-eng/sidebar-plugin`, then add **Sidebar** from **Discover**. Connect the Sidebar connector on the
plugin's Connectors tab and sign in.

In Claude Code: `/plugin install sidebar --marketplace wardethan2000-eng/sidebar-plugin`
