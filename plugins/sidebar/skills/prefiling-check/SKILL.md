---
name: prefiling-check
description: Check our own brief, motion, response or reply before it is filed. Checks every citation, quote and pin, good law on each authority, later cases that limit our key authorities, and what the other side will cite against us. Then reads the draft as opposing counsel and as the judge, checks record cites against the documents in the Project, and gives a fix list ordered by severity. The check is kept as a markdown file in the Claude Project; later drafts update it and show what was fixed and what is new. Use when the user shares our own draft and asks to check it, cite-check it, red-team it or find its weak spots, or asks whether it is ready to file. For the other side's brief, use brief-teardown. Needs the Sidebar connector.
---

# Pre-filing check

You can edit a brief on your own. What you can't do without Sidebar is check
its authorities against the opinions, find the later cases that cut them back,
and see what the other side will cite. This skill does that checking. Then it
reads the draft the way opposing counsel and the judge will, and turns it all
into one fix list, kept in a file that the next draft updates.

## 1. Find the file

First read `matter.md` and `facts.md` at the Project root, if they exist. They
hold the court, judge, parties, our side, the deadlines, the other work files,
and the facts so far, each with an id (F-001 and on) and a status. Formats and
rules: `references/matter-files.md`.

List the Project's files and look in `briefs/` for front matter
`skill: prefiling-check` with the same case and `document`. Names follow
`briefs/prefiling-<short-case-name>-<filing>.md`, e.g.
`briefs/prefiling-acme-sj-reply.md`.
- One match: this is a later draft. Read the file and go on as a re-check
  (step 9).
- Several could match: ask which one.
- None: start a new file from the template in step 9.

Read the work files on the same motion too: a `brief-teardown` file on the
brief ours answers, and the `motions/` analysis. Use what they found. Re-check
anything you rely on from them.

## 2. Ask once

Ask, in one message, only what you can't find in the Project, the file, or
`matter.md` and `facts.md`:
- **The draft:** a file in the Project or pasted text, and which version it is
  (a draft number or date).
- **What it is:** motion, response or reply; the court and judge.
- **What it answers:** their motion or brief, if it isn't in the Project.
- **The record it cites:** each transcript, exhibit or pleading the draft cites
  that isn't in the Project.
- **The deadline:** the filing date, or the date that starts the clock
  (service of their motion, an order).
- **The court's rules:** the local rule and the judge's own requirements on
  length, format and filing, as text or a file, if the user wants them checked.
- **Their strongest point,** if the user has a view.
- If `matter.md` or `facts.md` doesn't exist yet: anything its template needs
  that you can't find.

If the user wants to go on without something, list it under `missing`, and say
in the report what could not be checked without it.

## 3. Check every citation

Run `check_citations` on the full text of the draft. If it is over 400,000
characters, check it in sections. For each case it reports whether:
- the case exists;
- the name and year match;
- each quote appears word for word;
- each pin cite is on the right page;
- each short form resolves.

Every problem goes on the fix list. It doesn't check statutes or record cites:
steps 7 and 8 cover them.

## 4. Our authorities

**Good law, every case.** Run `check_good_law` on each case the draft cites,
string cites included. Read the SUBSEQUENT HISTORY first (reversed, vacated,
review granted), then each STRONG and POSSIBLE flag, then the LATEST WORD. If
the draft cites more than about 25 cases, do the key authorities first and say
which are left.

**Key authorities** are the 5–12 cases the main arguments rest on. For each one:
1. **What it holds, against what we say it holds.** Use `find_passages` with
   our proposition as the topic. Flag:
   - dicta stated as a holding;
   - a concurrence, dissent or plurality cited as the court;
   - a fact-bound result stated as a general rule;
   - "always" or "never" where the court hedged;
   - a quote cut or bracketed so that it says more than the court did.
2. **Later courts that cut it back.** Use `citing_cases(case,
   query='distinguish! OR limit! OR inapposite OR "not applicable" OR declin!')`.
   Run it again with a query built from the facts the other side will use to
   set our case apart.
3. **Binding or not.** An unpublished decision, or one from a court that
   doesn't bind the forum, cited as if it controls, is a fix. `get_case` gives
   the court and status; `search_cases` with `forum` labels results binding or
   persuasive.

## 5. What the other side will cite

- **Standard and elements.** Compare the draft's standard with
  `legal_standard(motion, forum)`, and each claim's elements with
  `claim_elements(claim, jurisdiction)`. Flag wording that is outdated, from the
  wrong court system (federal or Kansas state), or softened in our favor. The
  authorities those tools rank highest that the draft doesn't cite are the ones
  the other side will.
- **Adverse authority.** Run `search_cases` on each issue, with `court` set to
  the courts that bind the forum (`research_plan` lists them) and `forum` set,
  plus a fact-pattern query, for decisions that went the other way. Directly adverse binding authority the draft doesn't address is a
  fix: the other side will cite it, and counsel may have a duty to disclose it.
- **Facts like ours, the other way.** Run `citing_cases` on our key authorities
  with a query from our facts, for later cases on similar facts that came out
  against our position.
- **Their brief.** If we are responding or replying, list every argument and
  key authority in their brief that our draft doesn't answer.
- **This judge.** If the judge is known, run `search_cases` in the forum's
  court with the issue's terms and the judge's last name. Opinions carry the
  judge's name; confirm each hit is the judge's own ruling with `get_case`. A
  prior ruling by this judge on the issue, either way, goes in the report.

Confirm every quote and pin you attribute to them with `find_in_case` and
`check_pin_cite`. Never name a case you haven't opened.

## 6. Red team

Write this from what steps 3–5 found. Every authority you name must be one you
checked.

**As opposing counsel.** Their best 3–6 responses, strongest first. For each:
the argument, the authority they would cite, the part of our draft it attacks
(page or section), and whether the draft already answers it. Then list:
- concessions in our draft they can use;
- facts we state without a record cite;
- anything that contradicts our earlier filings in the Project, or a fact
  `facts.md` marks disputed or assumed;
- arguments raised for the first time in a reply.

**As the judge.** Read it cold, with the record and the other side's brief at
hand:
- Does the draft say exactly what order it wants?
- Is each issue tied to the standard and the elements, in order?
- Where does it stop persuading: an argument resting on one weak case, a string
  cite with no explanation, a fact with no cite?
- What 3–5 questions would the judge ask at a hearing?
- Do this judge's own rulings (step 5) cut for us or against us?

## 7. Record cites

For each record cite in the draft (complaint ¶, `Doe Dep. 45:3–12`, `Ex. 4 at
2`, a Bates number) whose document is in the Project:
- open the document at that ¶, page or line, and quote what it says;
- rate the cite **confirmed**, **wrong** (what it actually says, and the right
  cite if you find it), or **not in the Project**.

This is reading, not a tool check. If there are too many, check every cite in
the statement of facts and every cite an argument turns on, and list the rest
as not checked. Check each stated fact against `facts.md` too: a fact the draft
treats as undisputed that the ledger marks disputed or assumed is a fix.

## 8. What can't be checked

Say each of these plainly under "Not checked". Never fill the gap from memory.
- **Court rules:** page or word limits, fonts, formatting, certificates,
  conferral, proposed orders, the judge's own requirements. Check them only
  against rule text the user gave or that is in the Project, and quote it.
  Otherwise list what the user should check.
- **The deadline:** compute it with `compute_deadline` when the trigger date is
  known (`rule='list'` shows the periods it knows). Otherwise say it is unknown.
- **Statutes:** read each with the state statute tool (`kansas_statute`, with
  `as_of` when the law on a past date matters, or the Missouri, Colorado and
  Texas tools) and confirm any quote. Other statutes and regulations have no
  tool: list them.
- **Cases after the local copy's date** (the tools state it). A CourtListener
  search with `search_courtlistener` uses the user's daily requests: offer it
  for the key issues, and run it only if the user agrees.
- **Pins** that `check_pin_cite` marks unchecked, e.g. a regional-reporter page
  the copy lacks.
- **Record documents** that aren't in the Project.

## 9. Write or update the file

**Severity.**
- **Must fix:** wrong or unsupported. A citation that doesn't exist or names
  the wrong case; a misquote; a wrong pin; an authority reversed, overruled or
  abrogated on our point; a misstated holding; the wrong standard for the
  forum; a record cite that doesn't say what we say; a rule requirement missed,
  from rule text the user supplied.
- **Should fix:** a weakness the other side or the court will find. Adverse
  binding authority not addressed; a key authority later limited on facts like
  ours; an overstated holding; persuasive authority treated as binding; their
  strongest argument unanswered; a fact with no record cite.
- **Consider:** what would make it stronger. A newer or binding case for the
  same rule (the LATEST WORD); a weak case to drop; a string cite to explain or
  cut.

**A later draft:** run steps 3–8 on the new draft. An authority already checked
in the last run, and used for the same point, keeps its good-law and
later-courts results, unless the local copy's date has moved since (the tools
state it). Then go through the fix list:
- each open item is **fixed** (move it to Resolved, with the draft that fixed
  it), **still open**, or **no longer applies** (the text is gone; move it to
  Resolved and say so);
- each problem not on the list before is added, marked **new**, with the next
  free number. Items keep their numbers across drafts.

At the top of the Changes log, add a dated entry: the draft checked; how many
items were fixed, still open and new; and each new must-fix item.

**New file:**

```markdown
---
skill: prefiling-check
case: <caption>
court: <court>
judge: <if known>
document: <the filing, e.g. response to motion to dismiss>
side: <our party's role, e.g. plaintiff>
answers: <their motion or brief, if any>
due: <filing deadline, from compute_deadline or the order | unknown>
drafts:               # each draft checked, newest first
  - <file name, or "pasted"> (<version or date>), checked <YYYY-MM-DD>
last_run: <YYYY-MM-DD>
sources:              # every Project document read, with its date or version
  - <file name> (<date>)
missing: [<documents asked for and not provided>]
---
# Pre-filing check: <document>

## Changes
- <YYYY-MM-DD>: <first check, draft 1 | draft 2: 7 fixed, 2 still open, 3 new>

## Verdict
<ready to file | ready once the must-fix items are done | not ready>, in 2–4
sentences, with the biggest risks.

## Fix list
| # | Severity | Where (draft page or section) | Problem | Fix | Source (quote, pin) | Status |
|---|---|---|---|---|---|---|
<open items: must fix first, then should fix, then consider; status open or new>

## Resolved
| # | Problem | How resolved | Draft |
|---|---|---|---|

## Citation check
<check_citations summary on the draft, and the date it ran>

## Our key authorities
### <Case> — we use it for: "<our proposition>" (draft p. <n>)
- Holds: <quote, pin>
- Good law: <check_good_law result>
- Later courts: <limiting or distinguishing cases, each with its passage>
- Binding here: <yes | no, and why>

## What the other side will cite
| Authority | For | Binding here | Quote and pin verified | Does our draft answer it? |
|---|---|---|---|---|

## Red team: opposing counsel
## Red team: the judge
## Record cites
| Draft cite (draft p.) | Draft says | Record says (quote) | Result |
|---|---|---|---|

## Standards and elements: the draft vs. the courts
## Not checked
## Authorities
| Authority | Used for | Quote verified | Pin verified | Good law |
|---|---|---|---|---|
```

Don't edit the draft unless the user asks. If they do, save the revised draft
as a new version and keep the old one.

## 10. Check, save, report

1. Run `check_citations` on our file too: every case cited in it must check out.
2. Save the file with the Projects tool at its path. On a later draft,
   overwrite the same file.
3. Update the matter files, creating either one from its template in
   `references/matter-files.md` if it doesn't exist. Re-read each just before
   saving it.
   - `facts.md`: if a record document you read shows a fact is wrong or
     disputed, update its status and say why. Add a new fact only if this
     check relied on it.
   - `matter.md`: this file's row in Files (verdict and open must-fix count,
     today's date); the filing deadline in Deadlines, if you computed it; a
     dated line at the top of the Change log.
4. In the chat, give:
   - the verdict;
   - every must-fix item;
   - for a later draft: what was fixed, what is still open, what is new;
   - what could not be checked.
