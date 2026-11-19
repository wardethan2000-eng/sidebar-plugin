---
name: summary-judgment
description: Analyze summary judgment from either side: whether we can move, should move, or where we are exposed on theirs, element by element against the record. Draft or answer a statement of uncontroverted facts, and keep the analysis as a markdown file in the Claude Project that each later run updates, reporting what moved. Use when the user asks about summary judgment readiness, wants to move for or oppose summary judgment, asks for a statement of facts or a response to one, or asks to update an existing summary judgment analysis. Needs the Sidebar connector.
---

# Summary judgment analysis

You already know Rule 56 and K.S.A. 60-256. This skill adds:
- one file per matter that is found and updated, with a dated record of what moved since the last run;
- the right documents asked for up front;
- record citations only to what was actually read;
- the standard, elements and cases taken from Sidebar's tools and checked.

## 1. Find the file

First read `matter.md` and `facts.md` at the Project root, if they exist. They
hold the court, judge, parties, our side, the deadlines, the other work files,
and the facts so far, each with an id (F-001 and on) and a status. Formats and
rules: `references/matter-files.md`.

List the Project's files and look in `motions/` for front matter
`skill: summary-judgment` with the same case. Names follow
`motions/sj-<short-case-name>.md` (moving or assessing) or `motions/sj-response-<short-case-name>.md`
(answering their motion).
- One match: read it, and treat this run as an update.
- Several could match: ask which one.
- None: start a new file from the template in step 5.

## 2. Ask once, before any analysis

Ask, in one message, only what you can't find in the Project, the file, or
`matter.md` and `facts.md`:
- The court and judge. Which side we're on. Which claims and defenses are at issue.
- The dispositive-motion deadline from the scheduling order, and whether discovery
  is closed.
- Documents. Name each one you expected and didn't find:
  - the operative pleadings;
  - deposition transcripts (with exhibits);
  - documents produced;
  - interrogatory answers and admissions;
  - expert reports;
  - declarations or affidavits;
  - if we're responding: their memorandum and statement of facts.
- The court's summary judgment rule text, if it isn't in the Project. In federal
  court that's the local rule (D. Kan. Rule 56.1); in Kansas state court, Supreme
  Court Rule 141. Use the version the user supplies or one you have fetched, not
  memory, for the format of the facts statement.
- For an update: only what is new since `last_run` (new transcripts, new
  productions, rulings, an amended pleading).
- If `matter.md` or `facts.md` doesn't exist yet: anything its template needs
  that you can't find.

If the user wants to proceed without a document, mark it **missing** and treat
the facts that depend on it as unknown.

## 3. Ground it in Sidebar

- **Standard:** `legal_standard("summary judgment", forum)`. Use its binding wording
  and authorities. Don't state the standard from memory.
- **Elements:** call `claim_elements(claim, jurisdiction)` for every claim and
  affirmative defense at issue. Note which side bears the burden of proof at
  trial on each element: it decides what the movant must show.
- Confirm every quote with `find_in_case` and every pin with `check_pin_cite`.
  Run `check_good_law` on each case relied on beyond the standard.
- **Contested legal points** (a doctrine that could defeat an element): use
  `research_plan` and `search_cases`, with `citing_cases` and a fact query to find
  cases on similar facts.

## 4. Do the analysis

For each claim, and each element of it:
- **Evidence for, and evidence against,** each with a pinpoint record cite:
  - depositions as `<Name> Dep. <page>:<line>–<line>`;
  - exhibits as `Ex. <n> at <page>`;
  - Bates numbers where there are any.
- **Rating:** **solid**, **thin**, **disputed** (a genuine dispute on the record),
  **missing**, or **unknown**.

Cite only transcript pages and exhibits you read in this run. Anything else is
"unknown: not read", never a guess at a page. In this file, tag evidence with
its `facts.md` id where there is one: `F-012 (Doe Dep. 45:3–12)`. Never put
F-ids in the facts statement or the response: those are for filing.

Then give the overall read:
- **Can we move:** every element of the claim or defense solid, with no genuine dispute.
- **Should we move:** cost, the risk of educating the other side, and timing
  against the deadline.
- **Exposure on their motion:** which of our elements are thin or missing, and
  what evidence would fill each one.

**Facts statement (when drafting a motion):** one numbered fact per paragraph,
each with its record cite, in the format the court's rule requires.

**Response (when answering their statement):** go paragraph by paragraph:
- uncontroverted;
- controverted, with the record cite that controverts it;
- or immaterial.

Then add our additional facts.

## 5. Write or update the file

**Updating:** change only what moved, in place. At the top of the Changes log,
add a dated entry:
- the new documents read;
- each element whose rating moved, old → new, with the record cite that moved it;
- deadlines that are now close.

**New file:**

```markdown
---
skill: summary-judgment
case: <caption>
court: <court>
judge: <if known>
side: moving | responding | assessing
claims: [<claims and defenses at issue>]
deadline: <dispositive-motion deadline, if known>
last_run: <YYYY-MM-DD>
sources:            # every record document read, with its date or version
  - <file name> (<date>)
missing: [<documents asked for and not provided>]
---
# Summary judgment: <case>

## Changes
- <YYYY-MM-DD>: <first run | what moved>

## Short answer
<can we move / should we / where we're exposed, in 2–4 sentences>

## Standard
<from legal_standard: binding wording, quoted, with pins>

## Claims and defenses
### <Claim> — elements per <case, pin>; burden at trial: <party>
| Element | Evidence for (cite) | Evidence against (cite) | Rating |
|---|---|---|---|

## Exposure and what would fill it
## Statement of uncontroverted facts (draft) / Response to their statement
## Open questions
## Authorities
| Authority | Used for | Quote verified | Pin verified | Good law |
|---|---|---|---|---|

## Citation check
```

## 6. Check, save, report

1. Run `check_citations` on the file (case citations; it doesn't check record cites).
   Fix what it flags.
2. Save with the Projects tool at the file's path, overwriting it on an update.
3. Update the matter files, creating either one from its template in
   `references/matter-files.md` if it doesn't exist. Re-read each just before
   saving it.
   - `facts.md`: add the facts the element ratings rest on, with the next free
     ids. Move each status the record moved: **admitted** when the other side
     admits it; **disputed** when the record has evidence against it;
     **proven** when record evidence you read shows it and nothing you read
     contradicts it. Say when and why in Notes.
   - `matter.md`: this file's row in Files (one-line status, today's date); the
     dispositive-motion deadline in Deadlines; a dated line at the top of the
     Change log.
4. In the chat, give:
   - the short answer;
   - what moved since the last run;
   - the deadline, if it is close;
   - open questions, and documents still missing.
