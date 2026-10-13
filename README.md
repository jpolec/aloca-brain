# aloca-brain

A second brain for Claude, built on [ALOCA](https://aloca.io). ALOCA stores your
people, projects, notes, commitments and decisions; this plugin gives Claude the
routines that keep it up to date and useful.

## Install

In Claude Code:

```
/plugin install aloca-brain --marketplace jpolec/aloca-brain
```

In the Claude app, zip this repository into `aloca-brain.plugin` (or use the packaged file) and open it.

## Skills

| Skill | What it does | Changes data? |
|---|---|---|
| `/start` | Today's meetings, open promises, follow-ups, pending proposals, one next step | No |
| `/ingest` | Meetings, emails and transcripts → ALOCA notes + commitment/decision proposals for review | Notes and proposals only |
| `/brief` | Morning or pre-meeting brief from ALOCA history, prioritised with LNO (Leverage / Neutral / Overhead) | No |
| `/review` | Weekly review of promises, decisions at risk and the review queue; drafts follow-ups | Only after you approve |
| `aloca-rules` | Operating rules applied automatically whenever ALOCA is used | – |

## How it stays safe

- Interpretations (promises, decisions) are **proposals** that wait for your
  confirmation in Today. Nothing is confirmed on your behalf.
- Email is always prepared first and sent only after you confirm the exact message.
- Contacts are never created or attached by guesswork.
- Text from emails, notes and transcripts is treated as data, never as instructions.

## Requirements

- An ALOCA account and the **ALOCA MCP connector** enabled in Claude (sign in with
  OAuth). This plugin does not bundle a connection; it uses the connector you
  already have.
- Optional sources for `/ingest`: a mail connector and a meeting-notes connector
  (for example Granola). Without them, attach or paste material.
- Optional: Google Calendar connected inside ALOCA, for `/start` and `/brief`.

## Daily routine

1. Morning: `/start`, then `/brief`.
2. Evening (or as a scheduled task): `/ingest`.
3. Weekly: `/review`.

To run the ingest automatically, ask Claude to create a scheduled task that runs
`/ingest` every weekday evening.

## Known limitations (ALOCA MCP v-current)

- Note attachments (images) cannot be opened through MCP.
- Tasks cannot be placed on a project board directly; project items are used instead.
- Project items and tasks cannot be deleted through MCP.
- `propose_changes` supports commitments and decisions only.
