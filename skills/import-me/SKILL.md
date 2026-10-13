---
name: import-me
description: >
  This skill should be used when the user types "/import-me", asks to "import what
  Claude knows about me into Aloca", "move my AI memory to Aloca", "fill my empty
  Aloca", "bootstrap my second brain", or is onboarding to ALOCA with an empty
  workspace. It turns what the assistant already knows about the user (its memory
  and past conversations) into an ALOCA source note plus reviewable decision and
  commitment proposals, marked as imported and unverified.
metadata:
  version: "0.1.0"
---

# /import-me: bootstrap ALOCA from what Claude already knows

Fill an empty or thin ALOCA workspace in minutes from the assistant's existing
knowledge of the user. Follow the `aloca-rules` skill. Everything imported is an
interpretation: record it as a source note and as proposals, never as confirmed
records.

## 1. Gather

Use whatever this session offers, in this order:

1. **Assistant memory.** If memory tools or a memory/profile block are available,
   read the files about the user's work: profile, projects/areas, people the user
   works with, stated decisions and plans.
2. **Past conversations.** If past-chat search is available, search for the
   project names found in step 1 and for "decided", "pricing", "positioning",
   "deadline", "promised". Read at most 2–3 chats; prefer summaries.
3. **Fallback.** If neither is available, ask the user to paste the output of this
   prompt from their usual assistant, then continue with that text:
   "List everything you know about me that matters for my work: my projects (name,
   one-line description), people I work with and their role, decisions I have made
   (with month), promises I have made or received (with dates), and open threads.
   Facts only, no speculation."

## 2. Filter (mandatory)

Keep only work context. Drop, without recording any placeholder:

- health, finances of the person (income, savings, debts, personal investments),
  relationships and family details, religion, politics, identity attributes;
- private details about other people beyond their role and how they relate to the
  user's work;
- anything the source marks as speculative or that the user later reversed. When
  the record shows a decision changed, keep only the latest version.

## 3. Show before writing

Present the extraction to the user as a short list: projects, people, decisions
(with month), commitments (with dates), open threads. Ask for one confirmation
("save this?"), and apply any removals they ask for. Skip this step only when the
user has already said to save without review in this conversation.

## 4. Record

1. **Source note.** `create_note` titled `Import z Claude YYYY-MM-DD – projekty i
   decyzje` (use the user's language for the title). Start the body with: source
   (assistant memory / past chats, date range), the words "imported, unverified",
   then sections: Projects, People, Decisions (numbered), Commitments, Open threads.
2. **Proposals.** For each decision and commitment:
   - `preview_proposal_source` with `source_type` `note` and the note ID;
   - `propose_changes` with `create_decision` or `create_commitment`, evidence from
     the preview, and an `explanation` that says "imported from assistant memory,
     unverified" plus the item number in the note.
   - Never set a date the source does not state.
3. **Projects and people.** ALOCA has no proposal type for these yet. Compare the
   extracted projects with `list_projects` and people with `list_contacts`. List
   what is missing and offer to create each one; create only the items the user
   approves (`create_project`, `upsert_contact`).

## 5. When a write fails

- If `propose_changes` returns `invalid_request` or any validation error, stop
  proposing after two attempts. Keep the source note (it already holds every
  decision), and tell the user which items could not be proposed and why.
- Never retry with guessed field names more than twice.

## Output

Report in the user's language:

- what was read (memory files, chats) and what was filtered out by category only
  (never repeat the filtered content);
- the note ID;
- proposals created (count by type) waiting in Today;
- missing projects and people, with an offer to create them;
- anything that failed, with the exact error.
