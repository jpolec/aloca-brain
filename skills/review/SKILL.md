---
name: review
description: >
  This skill should be used when the user types "/review", asks for a "weekly
  review", "review my commitments", "what did I promise this week", "clean up my
  second brain", or "which decisions lack context". It reviews ALOCA commitments,
  decisions, follow-ups and pending proposals, and prepares follow-up emails for
  approval without sending anything.
metadata:
  version: "0.1.0"
---

# /review: weekly review

Keep the second brain honest: nothing promised is forgotten, every decision keeps
its reasons, and the review queue does not pile up. Follow the `aloca-rules` skill.

## Steps

1. **Pending proposals.** `list_proposals` (state `pending`). Group by age. Remind
   the user that they confirm or reject these in Today in ALOCA; list the oldest
   first with their explanation.
2. **Commitments.** `list_commitments` (state `active`), all pages. Sort into:
   - Overdue, I owe
   - Due in the next 7 days, I owe
   - Overdue, owed to me (candidates for a nudge)
   - No due date (ask whether to set one)
3. **Decisions.** `list_decisions` from the last 30 days. Flag decisions with no
   linked source or no stated reason: these are the ones that will lose their
   context. For each, suggest which note or meeting could supply the reason.
4. **Follow-ups.** `list_follow_ups`. Merge with the "owed to me" list, removing
   duplicates.
5. **Draft nudges.** For up to 5 of the most important "owed to me" items, draft a
   short, friendly follow-up in the language of the original conversation. Present
   all drafts together. Only when the user picks one, call `list_email_senders`,
   then `prepare_email`, show the full review (From, To, subject, body), and wait
   for explicit confirmation before `send_email`.
6. **Housekeeping offers.** Offer, but do not perform without approval: marking
   completed tasks or project items done, setting missing due dates, adding missing
   contacts found during `/ingest`.

## Output

- **Review queue:** N pending (oldest first, max 10 lines).
- **I owe:** overdue, then this week.
- **Owed to me:** with suggested nudges.
- **Decisions at risk:** decision, what is missing, where the reason might be.
- **Proposed actions:** a numbered list the user can approve by number
  ("do 1, 3, 4").

Cite record IDs throughout. Send nothing and change nothing until the user approves
specific numbered actions.
