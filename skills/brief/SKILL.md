---
name: brief
description: >
  This skill should be used when the user types "/brief", asks for "my morning brief",
  "prep me for my meetings", "brief me before the call with X", "what should I focus
  on today", or wants context before a meeting or event. It builds a pre-meeting
  brief from ALOCA history and the calendar, prioritised with Shreyas Doshi's LNO
  framework (Leverage, Neutral, Overhead).
metadata:
  version: "0.1.0"
---

# /brief: LNO morning and pre-meeting brief

Prepare the user for today (or one named meeting) using what ALOCA already knows.
Read only. Follow the `aloca-rules` skill.

## Scope

- No argument: every meeting today, plus the day's open promises.
- A person, company or meeting name: only that meeting or that person.

## Steps

1. **Meetings.** `list_calendars` then `list_events` for today (or the named
   meeting's day) in the calendar's timezone. Follow all pages.
2. **People.** For each attendee, resolve the contact with `list_contacts` by email.
   For matched contacts, call `get_context` with `entity_type` `contact` and read
   the recent notes, messages, commitments and decisions linked to them. Note any
   attendee who is not in ALOCA.
3. **Projects.** If a meeting clearly belongs to a project (title or linked
   context), call `get_context` with `entity_type` `project` for it.
4. **Promises.** `list_commitments` filtered by each matched contact: what the user
   owes them and what they owe the user, with due dates.
5. **Classify with LNO.** Label every meeting and every open promise:
   - **L (Leverage):** outcomes that create outsized, compounding value (a key
     decision, a relationship that unlocks revenue, hiring, strategy). Recommend
     preparing properly.
   - **N (Neutral):** needs to be done well but returns are linear. Recommend
     standard preparation.
   - **O (Overhead):** necessary but low value (status syncs, admin). Recommend
     minimal effort, delegating, or declining.
   Base the label on the evidence gathered, state the reason in one short phrase,
   and mark it as a suggestion. When unsure, choose N.

## Output

Order by LNO, then by time.

For each meeting:

- **Time · title · L/N/O** (reason)
- **Who:** names, role or company, last interaction and its date (with record IDs).
- **Open promises:** what the user owes them; what they owe the user.
- **Relevant decisions:** recent decisions touching this person or project.
- **Goal for this meeting:** one sentence.
- **Suggested opener or question:** one line.

End with:

- **Today's L items:** at most 3, the things worth the user's best energy.
- **O items to minimise:** one line each.
- **Gaps:** attendees not in ALOCA, meetings with no history. Offer `/ingest` or
  adding contacts.

Keep the whole brief readable in under three minutes. Do not pad empty sections.
