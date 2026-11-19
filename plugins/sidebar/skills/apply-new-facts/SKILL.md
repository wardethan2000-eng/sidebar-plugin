---
name: apply-new-facts
description: Apply a new or changed fact to the research already saved in the Claude Project. Records the fact in facts.md, finds every theory and memo in research/ that rests on it or should have, re-evaluates each one, logs what moved and why, revives dead theories whose conditions are now met, and proposes the new rabbit holes the fact opens. Use when the user reports a new fact, a client's answer, a new document, testimony or evidence, a fact that turned out wrong, or asks how a development affects the theories or research. Needs the Sidebar connector.
---

# Apply new facts to the research

You can see how a fact bears on an argument you are looking at. What you
won't do on your own is check every theory and memo saved in earlier chats,
including the ones that never mentioned this fact but should have. Nor will
you record what moved, so the next chat can trust the files. That is this
skill's job.

The research files follow the `open-research` skill's formats. Keep the shape
the existing files already have.

## 1. Load

Read `matter.md`, `facts.md`, `research/index.md`, `research/theories.md` and
`research/dispelled.md`. These are short. Don't open memos yet.

If there is no `research/` folder, record the fact (step 2), say there is no
research to apply it to, and stop.

## 2. Record the fact

- **New fact:** add it to `facts.md` in that file's own format, with the next
  F-id, its source, the date learned, and its status (alleged, admitted,
  disputed, proven or assumed).
- **Changed fact:** keep its id. Update the text or status, and note old →
  new with the date.

Ask only if the source or status is unclear. A fact the user states without a
source is "alleged", with the source "user, <date>".

## 3. Find what it touches

Make one list, in four passes:
1. **Direct:** each theory and memo that lists the F-id (use the index's
   "Facts the research rests on" table, and search `research/` for the id).
2. **Triggers:** each theory's "would change if" line, and each dispelled
   theory's "revive if". Does the fact meet one?
3. **Should have:** for each live theory, walk its "what a court must accept"
   steps and its elements. Does the fact bear on one? If it does and the
   theory doesn't list the fact, that is a miss. Add the fact to the theory
   and to its memo.
4. **Other work files:** search `claims/`, `motions/` and `briefs/` for the
   F-id and the fact's key words. List the matches. Don't edit those files;
   their own skills update them.

If the connector offers `check_research_ledger`, run it on the index and
`facts.md`. It returns every dependent, including indirect ones.

## 4. Re-evaluate each hit

Go highest-ranked theory first. Open the memo, and read its Application and
"What would change this". Decide one of:
- **no effect:** log it, with one line on why;
- **strengthens** or **weakens:** edit the Application, bump the memo's
  `version`, apply the confidence caps, and log old → new;
- **flips:** rewrite the Short answer and Application, and update the
  theory's status. If it died, move it to `dispelled.md` with the authority
  and a new "revive if";
- **needs research:** the memo's own authorities don't cover the new facts.

**What you may do without asking:** re-applying the memo's existing law to
the new fact, and re-checking its authorities, are maintenance.

**What needs the lawyer's go:** new searches beyond the memo's own
authorities. Put them at the top of the queue, unless the user already said
to update the research.

**Fact status changes** apply the caps:
- alleged → disputed caps the memo at medium;
- anything resting on an assumed fact is at most low;
- a fact proved wrong flips or weakens everything resting on it.

**A revive-if that is met** moves the theory back from `dispelled.md` to
`theories.md` as **open**, not supported. Queue its re-test.

When a memo's version changes, mark every memo and theory resting on it
needs-recheck.

## 5. New rabbit holes

Run only this fact through the `open-research` fact hooks and lenses: new
claims, defenses, parties, remedies, fee-shifting, clocks.
- **A new date** starts new clocks. Run the limitations kill test now
  (`limitations_period` + `compute_deadline`), and report the result first.
- **Other new candidates:** run their cheapest kill test only if the lawyer's
  go covers it. Otherwise queue them, each with what turns on it and its
  size.

## 6. Check and save

1. Run `check_citations` on every memo you edited, fix what it flags, and
   update `verification` and `law_checked`. If the connector offers
   `verify_memo`, use it and save what it returns.
2. Save the edited memos, `theories.md`, `dispelled.md` and the index: its
   facts table, theory table, queue and `counts`.
3. Write one line per change in `log.md`, each naming the F-id.
4. Add a line to `matter.md`'s change log only if the plan changes.

## 7. Report

In the chat, in this order:
1. **Deadlines:** any clock the fact started or moved.
2. **What moved:** bad news first, each item old → new, with the memo and
   the reason.
3. **Revived theories.**
4. **Checked, no effect:** the ids, so the lawyer can see what was looked at.
5. **Queued:** new rabbit holes and re-research, each with what turns on it.
   Ask which to run.
6. **Other files:** the claims, motion or brief files that mention the fact
   and need their own update.
