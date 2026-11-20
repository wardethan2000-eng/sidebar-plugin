---
name: open-research
description: Open-ended legal research on a matter, kept as a growing set of short memos in the Claude Project's research/ folder that later chats build on. Spots issues in the facts, generates theories across practice areas (claims, defenses, other parties, procedural routes, remedies, fee-shifting), finds analogous cases under other doctrines and in other states, tries to kill each theory against the best adverse authority, researches the survivors one subquestion per memo, and hands back a ranked queue of next rabbit holes. By default a run is the scan only, and the user picks which theories get memos. Use when the user asks to dig into a matter or question, explore theories or angles, think creatively about options, find analogies or persuasive authority, write research memos, or continue, review or check on earlier research. Needs the Sidebar connector.
---

# Open-ended research

You can spot issues and reason by analogy. This skill adds what you don't do
on your own:
- a written method, so the obvious lenses are never skipped;
- kill tests before deep work;
- a `research/` folder that later chats build on instead of redoing;
- confidence that follows from the sources;
- every authority taken from Sidebar and checked;
- a budget the lawyer sets.

The file templates are in `formats.md`. The checklists and search templates
are in `angles.md`. Read each one when its step comes up.

## Rules that hold throughout

1. **Nothing is researched without the lawyer's go.** The request itself is
   the go for its scope (step 3). Everything else goes in the queue.
2. **The files are the memory.** Save each memo and update the index before
   starting the next one. Never treat chat history or memory as a record of
   research.
3. **Every authority you rely on came from Sidebar in this chat.** That means
   a search, a chase, or a check of a case you remembered. Quotes are
   verbatim, pins are checked, and good law is checked.
4. **A status changes only with a source:** a fact id (F-), a memo (Q- with
   its version), or an authority. The lawyer's view, or yours, is recorded
   beside the status and moves nothing.
5. **Kill before you build.** Try the cheapest way a theory dies before
   researching it deeply.
6. **One bar for good and bad news.** Report what hurts the client first, and
   as plainly.
7. **An unresearched legal premise used by a second memo becomes a question**
   in the queue.
8. **Edit only `research/`,** plus the research row and change log in
   `matter.md`. Flag other files; their own skills update them.

## 1. Load, small first

1. Read `matter.md` (goal, side, forum, posture, deadlines) and
   `research/index.md`. If the index doesn't exist, this is the first run.
2. Read `facts.md`. It is the only source of F-ids.
3. After that, open only what the task needs: `theories.md` for theory work,
   `dispelled.md` before proposing theories, and memos by id along the
   question tree or the dependency chain. Never open every memo.
4. Every research file ends with an end line (`<!-- end … -->`). If a file you
   read has none, or the index's rows don't match its `counts`, the read was
   partial. Read it again by its path, or say so.
5. **Drift check.** Compare the index's "Facts the research rests on" table
   with `facts.md`. Any fact whose status or text changed is swept first
   (`apply-new-facts`, step 3 on). Then list the memos marked needs-recheck,
   and those whose `data_through` is older than Sidebar's data date.

## 2. Ask once

Ask, in one message, only what you can't find in the files:
- **Goal:** what the client wants out of this (money, stop the conduct,
  defeat a claim, leverage, a ruling). Generate angles only for that goal.
- **Side, forum and governing law** (or the candidate forums).
- **Scope.** The default run is the scan only: it ends with the ranked list,
  and memos are written only on the items the user picks. If the request
  already says how deep to go ("and research the top 3", "go deep on the
  best two"), that is their pick.
- **Facts and documents the theories will turn on** that `facts.md` lacks,
  especially the contract and its clauses, and the key dates.

If the user wants to go on without them, treat what depends on them as
unknown, or as assumed facts marked "assumed".

## 3. Decide what this run covers

| The user says | This run does |
|---|---|
| "dig into X", "find our theories", "research X", "what are the angles" | the scan (step 4), then stop: report the ranked list (step 7) and ask which to research. No memos |
| "research the top N", "scan and go deep on the best ones" | the scan, then memos on the top N survivors (4 if no number is given) |
| "go deep on T-003" | up to 4 memos inside that theory's questions |
| "run 1, 3 and 5" | exactly those queue items |
| "go, up to N" | N memos, about 4 per chat; then stop and say to continue in a new chat |
| "continue research" | approved but unfinished items, then needs-recheck memos; then report and ask |
| "where are we" | report from the index; no research |

Add a row to the index's Runs table (R-id, date, scope, budget) before you
start.

**Without asking, you may:** pick searches and cases, open subquestions under
an approved question (at most two levels deep), kill theories with recorded
authority, and re-verify stale memos you rely on.

**Ask first before:** a new theory family outside the goal, going over budget,
more than 3 `search_courtlistener` calls, or editing files outside
`research/`.

## 4. Scan: issues, theories, kill tests

Follow `angles.md`. In short:
1. **Fact hooks.** Tag each relevant F-id with what it could trigger
   (relationship, transaction, conduct, harm, party status, document clause,
   date).
2. **Lenses.** Walk each lens: claims, defenses (ours and theirs), other
   parties, procedural vehicles, remedies, fee-shifting, regulators and
   insurance, constitutional (government actor only). **Write every candidate
   down before judging any.** Theories the other side will run are tracked
   too ("for: them").
3. **Analogy.** Strip the dispute to its shape. Run 2–3 structure searches
   across doctrines and states (`angles.md` has templates). Any doctrine a
   court used on facts like these is a candidate.
4. **De-duplicate.** Check `theories.md` and `dispelled.md`. Don't re-propose
   a dead theory unless its "revive if" is now met. If `claims/` holds a
   potential-claims file, its claims are theories already: link them and
   don't redo their elements or deadlines. Its rejected claims count as
   dispelled.
5. **Kill tests.** For each candidate, run the 1–3 cheapest disproofs now:
   limitations (`limitations_period` + `compute_deadline`), an element the
   facts can't meet (`claim_elements`), a contract clause, a binding
   rejection (adverse search), a doctrine bar, a remedy that isn't available.
   - **killed:** goes to `dispelled.md` with the authority and a "revive if";
   - **wounded:** marked weakened;
   - **survived:** stays open.
6. **Rank** by viability and value, then cost (rubric in `angles.md`). Long
   shots stay listed. Each one says how far the leap is (binding, persuasive
   or analogy) and what a court would have to accept.

Save `theories.md`, `dispelled.md` and the index now, before any deep
research. If this run is the scan only (the default), go to step 7: the
ranked list is the queue, and the user picks what gets a memo.

## 5. Deep research: one subquestion per memo

For each item in scope, best-ranked first:
1. **Write the question neutrally:** 20 words at most, no client names. Give
   it a Q-id, its parent and its theory.
2. **The law, facts set aside.** Call `research_plan(question,
   jurisdiction)` and follow it:
   - the statute first;
   - discovery searches, even for cases you remember;
   - the forward chase (`citing_cases` with `query=`) and the backward chase
     (`cited_authorities`);
   - holdings read with `find_passages`;
   - `check_good_law`'s LATEST WORD;
   - adverse authority.

   Write the Rule section from this pass: verbatim quotes, pins, binding or
   persuasive, and no F-ids.
3. **Cases like ours, both outcomes:** fact-pattern searches and
   `citing_cases(leading case, query=<facts>)`.
4. **If the forum is silent,** climb the persuasive ladder in `angles.md`, in
   order, and label each authority with its leap.
5. **Apply** the law to the facts by F-id. Say which facts carry the answer
   and which change would flip it.
6. **Against us:** the other side's best 2–3 arguments, each with the best
   authority an adverse search found, and whether it is answered. If the
   search found nothing, give the queries.
7. **Rely on other memos by reference only** (`rests_on_memos: [Q-004 v1]`).
   First check that the memo is final, its law check is current and its facts
   are unchanged. Never copy its quotes; re-pull them with `find_in_case`. A
   memo can't be more confident than a memo it rests on.
8. **Stop when one of these holds, and say which:**
   - **answered:** binding authority verified, latest word read, adverse
     authority answered;
   - **saturated:** three different discovery approaches, plus the forward
     chase of the two leading cases, found nothing new. Answer "unsettled" and
     record the dead ends;
   - **can't matter:** neither answer would change the theory's rank;
   - **budget:** the budget is spent. Mark the memo draft and say what is
     left;
   - **depth:** two levels below the approved question. The rest goes to the
     queue.
9. **Side findings** (law that bears on something else) and **open
   questions** go to the queue, not into this memo's answer.

Sidebar's data ends at a fixed date (the tools say which). Use
`search_courtlistener` only when a question turns on later decisions; it
spends the user's CourtListener requests. Copy every Coverage notice into the
memo. "Nothing found" in a known hole is not "no authority".

## 6. Check and save each memo before the next

1. Run `check_citations` on the whole memo. Fix what it flags and run it
   again. Run `check_good_law` on each authority the answer rests on. Record
   both in `verification`, with the date. If the connector offers
   `verify_memo`, run it instead and save what it returns.
2. Save `research/memos/Q-<nnn>-<slug>.md` with the Projects tool. When
   updating a memo, overwrite it, bump `version`, and add a Changes line.
3. Update the other files:
   - `theories.md`: status, confidence (apply the caps in `formats.md`) and a
     history line with its source;
   - `dispelled.md`, if a theory died;
   - `index.md`: the tree, the theory table, premises, the facts map, the
     queue, the run row and `counts`;
   - `log.md`: one line per change, each naming its source.

   A memo whose version changed marks every memo and theory resting on it
   needs-recheck.
4. If the connector offers `check_research_ledger`, run it on the index and
   `facts.md`, and fix what it flags.

Then start the next item.

## 7. Report and hand back

In the chat, in this order:
1. **What changed:** theories by status, bad news first, each with its memo
   and authority. Put any deadline the research found first of all.
2. **Each question answered,** in one line, with its confidence.
3. **What couldn't be verified,** and any Coverage notices.
4. **Budget used,** and where the run stopped. After a scan only: how many
   candidates, how many killed (each with its authority), how many alive.
5. **The queue:** ranked, each with what turns on it and its size (S, M or L).
   End by asking which to run.

Update `matter.md`'s research row. Add a line to its change log only when a
theory's status changes the plan. If a supported or dispelled theory is a
claim in `claims/`, say that file needs updating. Don't edit it.
