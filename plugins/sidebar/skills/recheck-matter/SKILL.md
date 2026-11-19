---
name: recheck-matter
description: Re-check every authority the matter's work files rely on, when the user asks. Runs good law again, finds new cases citing them since the last recheck, statutes amended since, and newer statements of the standards and elements. Reports what changed and which files and conclusions it affects, and offers updates; it never edits a finding on its own. Records the recheck in matter.md. Use when the user asks whether anything has changed in the law for the matter, whether our cases are still good law, to recheck or refresh the authorities, or "anything new?". Runs only when asked, never on a schedule. Needs the Sidebar connector.
---

# Matter recheck

Each Sidebar work file checked its authorities when it was written. The law
moves after that: a case is reversed or limited, a new binding decision comes
down, a statute is amended. This skill looks again at every authority in the
matter at once and tells the user what moved and where it matters.

Two rules:
- **It only flags and offers.** It never changes a finding, a rating or a
  conclusion in a work file on its own. The user decides what to update.
- **It runs only when the user asks.** Never schedule it or set up a recurring
  run.

## 1. Read the matter

Read `matter.md` and `facts.md` at the Project root (formats and rules:
`references/matter-files.md`). From `matter.md`'s front matter, take
`last_recheck` and `checked_through`: the local case-law copy's date at the
last recheck. If `matter.md` doesn't exist, you will create it in step 7; ask
only for what its template needs that you can't find.

List the work files: everything in `claims/`, `motions/`, `briefs/` and
`research/`, and any other work file in `matter.md`'s Files index. Leave out
source documents (pleadings, transcripts, exhibits): they hold no findings.

Look for `rechecks/recheck-<short-matter>.md` (front matter
`skill: recheck-matter`). If it exists, it holds the last recheck's results,
the baseline for this one. If not, this is the first recheck.

## 2. Gather the authorities

For each work file:
1. Run `check_citations(text, table_of_authorities=true)` on it. It gives each
   case's id and parallel cites, so a case cited different ways in different
   files counts once. It uses no CourtListener requests. Note any case it now
   flags.
2. From the file's Authorities table and text, note what the file uses each
   case for, and which conclusion rests on it: a claim's rating, an element,
   the standard, a limitations rule, a holding an argument needs.
3. Note the statutes it lists.

Build one list: each case and its id, every file that cites it, what for, and
whether a conclusion rests on it (**key**) or it is support. Cases a file
lists as the other side's (a `brief-teardown` file's key authorities, a
`prefiling-check` file's "What the other side will cite") are **theirs**. Bad
news for their cases is good news for us.

Use the case id (`case 1234567`) in every later call. A case `check_citations`
couldn't find in the local copy goes under Not checked: don't pass it to the
other case tools, which would fetch it from CourtListener with the user's
requests. Ask the user about it instead.

If the list has more than about 30 cases, do the key ones first, say how many
are left, and offer to finish in a new chat in the same Project.

## 3. Set the window

`since` is the first day the last look could not see:
- **After an earlier recheck:** the day after `checked_through`. Use the copy's
  date, not the recheck date: cases filed between the two reach the copy
  later, with their own older dates.
- **First recheck:** nothing records what the earlier runs could see. Use six
  months before the earliest `last_run` among the files citing the case. Some
  results will already be in the files: skip those.

Every tool reply states the local copy's date ("through YYYY-MM-DD"). If it
still equals `checked_through`, the copy holds no newer decisions: say so and
skip step 4.2. Still do the rest: older decisions can be added to the copy
without its date moving.

## 4. Check each case

1. **Good law:** `check_good_law(case)`. Compare it with the last result (the
   recheck file, or the work file's Good law column). Look for new SUBSEQUENT
   HISTORY, a new STRONG or POSSIBLE flag, and new decisions in the LATEST
   WORD. Note the "cited by" count.
2. **New citing cases:** `citing_cases(case, since=<since>,
   order_by='newest')`. For a key case with many, run it again with
   `query='overrul! OR abrogat! OR disapprov! OR limit! OR distinguish! OR
   declin!'`, and again with a query from our facts.
3. **Older decisions added:** after an earlier recheck, if the cited-by count
   rose by more than the new citing cases found, older decisions were added to
   the copy. For key cases, run `citing_cases` once more without `since`, with
   the same negative-words query.
4. **Read before judging.** For each new case that could matter, use
   `find_passages` on it with the proposition our file relies on. Don't judge
   from a snippet.

## 5. Statutes, standards and elements

- **Kansas statutes:** `kansas_statute(section, as_of=<last recheck, or the
  file's last_run>)`. If the text in force then isn't the current text, the
  statute was amended since: read what changed. Then
  `cases_citing_statute(section, filed_after=<since>)` for new cases applying
  it.
- **Missouri, Colorado and Texas statutes:** those tools have no `as_of`.
  Compare the current text with any quote in the file.
- **Other statutes and regulations:** no tool. List them under Not checked.
- **Standards and elements a conclusion rests on:** call `legal_standard` or
  `claim_elements` again with `filed_after=<since>`. A newer binding statement
  in different words is a change worth reporting.

## 6. Sort what changed

Put each change in one group, with the files and conclusions it touches:
- **Affects a conclusion:** a key case reversed, overruled, abrogated, or
  limited on the point we use it for; a new binding decision on the issue that
  goes the other way, or on facts like ours; a statute amended in a part we
  rely on; a binding statement of a standard or elements that differs from the
  file's.
- **Worth reading:** new cases that discuss a key case with no negative signal;
  a newer binding case for the same rule (a better cite); bad news for one of
  their cases.
- **No change.**

For each change that affects a conclusion, write the proposed update: the
file, the section, what it says now, what it would say, and why (case, quote,
pin). Confirm each quote with `find_in_case` and each pin with
`check_pin_cite`.

Cases after the copy's date are outside every check above. Offer, but don't
run, a CourtListener search for the key cases: one
`search_courtlistener(query='"<citation>"', court=<binding courts>,
filed_after=<day after the copy's date>)` each. Say how many of the user's
daily requests it would use (1 each). Run only what the user approves.

## 7. Save, offer, record

1. Write or update `rechecks/recheck-<short-matter>.md` from the template
   below. On a later recheck, update it in place: replace "What changed",
   update the tables, keep old offers with their status, and add a dated entry
   at the top of Changes.
2. Run `check_citations` on it, then save it with the Projects tool.
3. Update `matter.md` (create it from its template if it doesn't exist).
   Re-read it just before saving.
   - front matter: `last_recheck` = today; `checked_through` = the copy's date;
   - Rechecks: a line such as "2026-12-01 (copy through 2026-09-30; since
     2026-04-01): 1 affects a conclusion, 4 worth reading — see
     `rechecks/recheck-<short-matter>.md`";
   - this file's row in Files, and a dated line at the top of the Change log.

   A recheck learns no facts: leave `facts.md` as it is.
4. In the chat, give:
   - what changed, grouped as in step 6, each with the files it touches;
   - the offered updates, numbered, each with the file and section and what
     would change, and a question: which should I apply?
   - the CourtListener search offer, if any, with its cost;
   - what was not checked.
5. **Only after the user says yes**, apply each approved update: edit that
   section of that file, add a dated entry to its Changes log ("recheck
   <date>: <what changed>, approved by the user"), re-run `check_citations` on
   it, and update its row in `matter.md`. Mark the offer applied in the
   recheck file, or declined if the user said no.

```markdown
---
skill: recheck-matter
matter: <short name, the same as matter.md>
last_run: <YYYY-MM-DD>
checked_through: <the local copy's date>
since: <YYYY-MM-DD; on a first recheck, the earliest one used>
files_checked:
  - <file> (last_run <date>)
---
# Recheck: <matter>

## Changes
- <YYYY-MM-DD>: <first recheck | n cases, since <date>: x affect a conclusion,
  y worth reading>

## What changed
### Affects a conclusion
- <case>: <what happened (new case, quote, pin)>. Touches `<file>`, <section>:
  <the conclusion>.
### Worth reading
- <case>: <what happened>. Touches `<file>`.

## Offered updates
| # | File and section | Now says | Would say | Why (case, quote, pin) | Status |
|---|---|---|---|---|---|
<status: offered | applied YYYY-MM-DD | declined YYYY-MM-DD>

## Authorities
| Case | Case id | Ours or theirs | Cited in | Key | Good law | Cited by | New citing since | Checked |
|---|---|---|---|---|---|---|---|---|

## Statutes
| Section | Cited in | Amended since? | New cases applying it | Checked |
|---|---|---|---|---|

## Not checked
<cases after the copy's date unless searched; cases not in the local copy;
statutes with no tool; anything left for a later chat>
```
