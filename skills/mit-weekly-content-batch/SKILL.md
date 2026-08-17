---
name: mit-weekly-content-batch
description: The operating procedure for MiT's weekly content engine. Run this skill whenever JC says "run the weekly content batch," "content batch," asks for next week's content packet, or wants to plan, produce, or review the week's LinkedIn and blog content. Also consult it when banking mid-week content ideas, updating the content calendar, or deciding what to cut in a compressed week.
---

# MiT Weekly Content Batch

Produces one week of Made In Tandem content in a single Friday session: all pieces drafted, gated, and staged for JC's review. JC reviews once (~45 minutes), approves or redirects, and posts day by day. Nothing ever publishes without JC's explicit sign-off; Claude is never the ship gate.

## The cadence

| Day | Piece | Channel | Voice skill |
|---|---|---|---|
| Monday | Announcement post for last Thursday's blog | JC personal | write-like-jc |
| Tuesday | Answer article + personal share post | MiT company page + JC personal | answer-article (which layers on write-like-jc) |
| Wednesday | Standalone post | JC personal | write-like-jc |
| Thursday | Thinking blog post + company announcement post | thinking.madeintandem.com + MiT company page | write-like-jc |
| Friday | Standalone post | JC personal | write-like-jc |

Priority order when a week compresses: drop Friday's post first, then Wednesday's. Never drop the Thursday blog or the Tuesday answer article; those compound.

## Notion locations (Brain 2 > Projects/Areas)

- **MiT Content Calendar**, data source `3391eed6-9752-8008-9aeb-000b2baaae61`. One row per piece per day. Status flow: Idea > Drafting > Reviewing > Scheduled > Published. Fill Post URL after JC publishes.
- **MiT Content Engine** hub page `3be1eed6-9752-8136-bbc1-fb68af51f29c`, containing:
  - **Content Fodder** database, data source `8d7e8985-5d4d-4bc2-b850-a8a13f022599`
  - **MiT Competitors** page (scan list with blog links)
  - **Answer Article Prompt Backlog** page

## Batch procedure

1. **Read state**: calendar rows for next week, Content Fodder (New and Queued), the prompt backlog.
2. **Scan for ideas.** Always scan, every batch: (a) JC's Recall knowledge base via the Recall search tools, for what he's been saving and thinking about; (b) the competitor blogs listed on the MiT Competitors page, for topics gaining traction, angles worth countering, and gaps nobody covers. Ideas don't have to come from these sources, but the scan is not optional. Bank anything promising in Content Fodder with Source set accordingly.
3. **Pick angles.** For the Thursday blog and Tuesday answer article: draft the top-pick angle in full and list two alternates in one line each. JC approves direction on substantial pieces; the drafted top pick keeps that gate fast. Wednesday and Friday posts draw from fodder first.
4. **Draft everything** per the voice skills. Editorial rules: client problem owns headlines; AI appears as tooling below the fold or not at all; critique practices, never firms or people; MiT is "AI-integrated," never "AI-first."
5. **Gate everything via bash**: em-dash grep (hard fail), banned vocabulary regex, prose colon counts, character/word counts, Honest Numbers claim audit, truncation check on post hooks (~200 chars).
6. **Update Notion**: calendar rows to Drafting with angle notes; fodder rows to Queued or Used.
7. **Deliver the packet** as one markdown file (each piece labeled with its day, channel, and character count), plus a short chat summary of the decisions JC needs to make.

## Mid-week fodder intake

When JC sends an idea at any time, in any format: immediately add a Content Fodder row (Source: JC, Status: New, his words in the Idea and Notes fields), confirm in one line, and move on. Do not expand it into drafts unless he asks.

## Standing constraints

- No auto-publishing anywhere, ever. JC posts by hand.
- Approval gates survive speed pressure: substantial new pieces get angle approval.
- Announcement posts take a different door than the linked piece's dek; the link card already shows title and description.
- Comments on others' posts are a separate motion (reconnect, diagnose, teach) and not part of the batch.
