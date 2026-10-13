---
name: ingest
description: >
  This skill should be used when the user types "/ingest", says "ingest my day",
  "file today's meetings into Aloca", "update my second brain", "process my emails
  into Aloca", or when a scheduled task asks for the daily ALOCA ingest. It turns
  meeting notes, emails and transcripts into ALOCA notes plus reviewable commitment
  and decision proposals, without changing confirmed records.
metadata:
  version: "0.1.0"
---

# /ingest: daily ingest into ALOCA

Turn the last day of conversations into ALOCA records the user can review. Follow
the `aloca-rules` skill. Never send email or change confirmed records here.

## Inputs

- Default window: the last 24 hours (or since the last ingest note, if one exists:
  search notes for the title prefix "Ingest ").
- Sources, in order of preference, using whatever is connected in this session:
  1. Meeting notes or transcripts (for example Granola, Fireflies, Otter, or files
     the user attached).
  2. Email threads (a connected mail tool).
  3. Anything the user pastes or attaches.
- If no source is connected, ask the user to attach or paste material. Do not
  invent content.

## Steps

1. **Collect.** Gather source items in the window. Skip newsletters, receipts,
   automated notifications and anything with no person on the other side.
2. **Select.** Keep only items that contain at least one of: a promise (someone will
   do something), a decision (something was chosen or ruled out), or new context
   about a person or project worth remembering. Drop the rest.
3. **Record the source.** For each kept item, create one note with `create_note`:
   - Title: `Ingest YYYY-MM-DD – <meeting or thread subject>`
   - Body: who, when, channel, a short neutral summary (max ~15 lines), then a
     "Promises" and a "Decisions" list quoting the shortest phrase that shows each.
     Keep a link or identifier to the original source.
   One note per meeting or thread, never one giant note.
4. **Resolve people.** For every person mentioned, look them up with
   `list_contacts` (by email first, then name). Record the match in the note text.
   Do not create contacts automatically: collect unknown people in a list for the
   summary.
5. **Propose.** For each promise and decision in the note:
   - Call `preview_proposal_source` with `source_type` `note` and the new note ID.
   - Call `propose_changes` with operation `create_commitment` or
     `create_decision`, the fields extracted from the text, and the evidence
     (source_type, source_id, source_revision, source_hash) from the preview.
   - Write a one-sentence `explanation` saying why this is a promise or decision.
   - Set due dates only when the source states one. Never guess.
   - Link the contact only when step 4 found exactly one match.
6. **Close the run.** Create one note titled `Ingest YYYY-MM-DD – summary` listing
   what was processed, the notes created, the proposals made, and the unknown people.

## Output

Report in the user's language:

- Sources read (count per type) and the time window.
- Notes created (IDs).
- Proposals waiting in Today: N commitments, M decisions.
- Unknown people: name, email, which meeting. Offer to create them as contacts; do
  so only after the user confirms each one.
- Anything skipped because a tool or source was unavailable.

## When run on a schedule

Nobody is watching. Do steps 1–6, never create contacts, never change confirmed
records, and put everything that needs a human decision into the summary note so
the user sees it in the next `/start`.
