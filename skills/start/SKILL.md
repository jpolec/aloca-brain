---
name: start
description: >
  This skill should be used when the user types "/start", says "start my day in Aloca",
  "what's open in Aloca", "where am I", "what should I do next" or opens a session
  with the aloca-brain plugin. It checks the ALOCA connection, shows today's meetings,
  open commitments, follow-ups and pending proposals, and recommends one next step.
metadata:
  version: "0.1.0"
---

# /start: entry point

Give the user a one-screen picture of where things stand and one recommended next
step. Read only; change nothing. Follow the `aloca-rules` skill.

## Steps

1. **Check the connection.** Call `whoami`. If it fails, tell the user the ALOCA
   connector is not reachable and stop.
2. **Today's schedule.** Call `list_calendars`, pick the primary calendar and its
   timezone, then `list_events` from now until the end of today in that timezone.
   Follow `next_cursor` until done. If no calendar is connected, say so and continue.
3. **Open promises.** Call `list_commitments` (state `active`). Split into
   "I owe" and "owed to me" using the `direction` field, and flag anything past due.
4. **Follow-ups.** Call `list_follow_ups`. Keep the top 5 by urgency.
5. **Pending review.** Call `list_proposals` (state `pending`). Count them; these are
   suggestions waiting for the user's confirmation in Today.
6. **Recent decisions.** Call `list_decisions` with `limit` 5 for context.

## Output

Keep it short, in the user's language:

- **Today:** meetings with time and who is in them (max 6 lines).
- **I owe:** overdue first, then due this week.
- **Owed to me:** anything overdue.
- **Follow-ups:** top 5, one line each with the reason.
- **Waiting for your review:** N proposals (link to Today in ALOCA).
- **Next step:** exactly one recommendation, chosen in this order:
  1. an overdue promise the user owes,
  2. pending proposals older than a day,
  3. the next meeting within 2 hours (offer `/brief` for it),
  4. otherwise offer `/ingest` if nothing was ingested today.

Cite record IDs. If a section is empty, write one line saying so rather than
dropping it.
