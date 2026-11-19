# The matter files: matter.md and facts.md

Every Sidebar skill shares two files at the root of the Claude Project:
- `matter.md` is the hub: the case, the parties, the next deadlines, an index of
  every work file, and a dated change log.
- `facts.md` is the fact ledger: each fact once, with an id, its source and its
  status. Work files cite facts by id.

This file is the same in every Sidebar skill folder.

## Rules for both files

- Read both at the start. Don't ask the user for anything they already say.
- If one is missing, create it from the template below when you save your
  work. Ask only for what the template needs that you can't find in the
  Project or the chat. (`recheck-matter` learns no facts, so it creates only
  `matter.md`.)
- Re-read a file just before you save it: another chat in the Project may have
  changed it since you read it. Add your lines and change only what this run
  changed. Leave everything else as it is.
- Save with the Projects tool at the root path (`matter.md`, `facts.md`),
  overwriting the file.
- Keep the `##` headings exactly as below, in this order. Other skills find
  sections by these headings. A skill that needs its own section adds it just
  above `## Change log`. Never rename, merge or drop a heading.

## matter.md

```markdown
---
file: matter
matter: <short name, e.g. Buyer v. Seller — defective home>
updated: <YYYY-MM-DD>
last_recheck: <YYYY-MM-DD | never>
checked_through: <the local case-law copy's date at that recheck | none>
---
# Matter: <short name>

## Case
- **Court:** <court | not filed (possible forums: ...)>
- **Case number:** <number | none yet>
- **Judge:** <name | not assigned | unknown>
- **We represent:** <party> (<plaintiff, defendant, ...>)
- **Posture:** <one or two lines: where the case stands now>

## Parties
| Party | Role | Side | Counsel |
|---|---|---|---|
| <name> | <plaintiff, defendant, third party, insurer ...> | ours / theirs / neither | <if known> |

## Deadlines
| Date | What | Basis | From |
|---|---|---|---|
| <YYYY-MM-DD> | <e.g. response to motion to dismiss due> | <compute_deadline: rule, trigger date; or the order and its page> | <file> |

## Files
| File | What it is | Status | Updated |
|---|---|---|---|
| `facts.md` | Fact ledger | <n facts, F-001 to F-0nn> | <YYYY-MM-DD> |
| `<folder>/<file>.md` | <e.g. Potential claims> | <one line> | <YYYY-MM-DD> |

## Rechecks
- none yet

## Change log
- <YYYY-MM-DD> — <skill name, or "user">: <what changed> (`<file>`)
```

How to keep each section:
- **Case:** update it when the posture changes (filed, answered, motion
  pending, ruled on, discovery closed, set for trial).
- **Deadlines:** the next few, soonest first. Every date comes from
  `compute_deadline` or from an order or rule text you read; never count days
  yourself. Add the deadlines your run computed. When one has passed, remove it
  and note that in the Change log. If `deadlines.md` exists, it is the full
  list: keep this section as its summary and never contradict it.
- **Files:** one row per work file in `claims/`, `motions/`, `briefs/`,
  `rechecks/` and any other folder of work files. `research/` gets one row
  only, for `research/index.md`, with its counts and last run; the research
  index lists the memos. The status is
  one line on what the file concludes or where it stands, e.g. "3 claims
  strong; nearest last day 2027-03-02" or "draft 2: 2 must-fix items open".
  Updated is the file's `last_run`. Add a row for any work file you see that
  the index lacks.
- **Rechecks:** the `recheck-matter` skill adds one line per recheck. Other
  skills leave it alone.
- **Change log:** newest first, one line per run or user change. Never edit or
  delete an old line.

## facts.md

```markdown
---
file: facts
matter: <short name, the same as matter.md>
updated: <YYYY-MM-DD>
---
# Facts: <short name>

Status: alleged | admitted | disputed | proven | assumed | disproved | withdrawn (defined below).
Cite a fact in a work file as its id with its source: F-012 (Compl. ¶ 12).

| ID | Fact | Source | Learned | Updated | Status | Notes |
|---|---|---|---|---|---|---|
| F-001 | <one fact, with its own date if it has one> | <document and ¶, page or page:line> | <YYYY-MM-DD> | <YYYY-MM-DD> | alleged | |

## Change log
- <YYYY-MM-DD> — <skill name, or "user">: <ids added or changed, and from what source>
```

Statuses:
- **alleged:** stated in a pleading, a letter, or by the client or the user,
  and not yet shown by evidence.
- **admitted:** the other side has admitted it: in an answer, a response to a
  request for admission, a stipulation, its own brief, or its own document.
- **disputed:** the other side denies it, or the evidence conflicts.
- **proven:** shown by record evidence (a deposition, a document, a
  declaration), or found by the court.
- **assumed:** taken as true for the analysis without a source. It must be
  confirmed; list it among the open questions.
- **disproved:** shown to be false by evidence or a ruling. Everything resting
  on it must be re-checked.
- **withdrawn:** the user or the client takes it back, or it was entered in
  error. Same as disproved for the files that rest on it.

How to keep the ledger:
- **One fact per row:** one act, statement, date or amount. Put the fact's own
  date in the fact ("On 2025-03-04, Seller delivered the home"). **Learned** is
  the date the fact entered the ledger. **Updated** is the date its text,
  source or status last changed (the same as Learned until then). Other skills
  compare it with their own run dates to spot facts that changed since.
- **Source:** the document and its ¶, page, or page:line, e.g. `Compl. ¶ 12`,
  `Doe Dep. 45:3–12`, `Ex. 4 (purchase agreement) at 2`. For what the user
  tells you: `user, chat YYYY-MM-DD`. Give only a source you read in this run.
- **Ids:** the next free number, F-001, F-002 and on. Never reuse, renumber or
  delete one: other files cite them. A fact that turns out to be wrong stays,
  with status **disproved** or **withdrawn** and the reason in Notes.
- **Before adding a fact, look for it.** If it is already there, add the new
  source to its row, or change its status, instead of adding a new id.
- **Status changes:** change Status, set Updated, and record the old status in Notes:
  "was alleged; admitted 2026-12-01 (Answer ¶ 12)".
- **What to add:** the facts the analysis relies on, not every sentence of
  every document.
- **Text for filing:** never put F-ids in a statement of facts, a brief or
  anything else meant for the court. Use the record cite there.
