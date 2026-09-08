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
| Tuesday | Answer article + company share post | JC personal profile (native LinkedIn Article) + MiT company page | answer-article (which layers on write-like-jc) |
| Wednesday | Standalone post | JC personal | write-like-jc |
| Thursday | Thinking blog post + company announcement post | thinking.madeintandem.com + MiT company page | write-like-jc |
| Friday | Standalone post | JC personal | write-like-jc |

Priority order when a week compresses: drop Friday's post first, then Wednesday's. Never drop the Thursday blog or the Tuesday answer article; those compound.

Channel note (changed Aug 31, 2026): Tuesday answer articles publish from JC's personal profile, not the company page; the company page posts the share. Drafts land in the repo at `linkedin-articles/YYYY/` with `channel: linkedin-jc`, and the share at `linkedin-posts/company/YYYY/` in company voice.

Batch day stays Friday. JC reserves calendar time for the review pass; the batch is the production run for the following week, so a skipped Friday means the next Tuesday's article gets rescued on publish day. Don't let that happen twice.

## Production horizon and the buffer (added Aug 31, 2026)

The engine's real goal is a 4-to-6-week buffer of finished long pieces, so heavy client weeks don't interrupt the flow. Two constraints shape everything:

- **JC's editing time is the bottleneck, not drafting.** Short posts he edits at post time (minutes). Answer articles and essays take him 2-3 editing rounds, 45-60 minutes total, spread across separate sessions with fresh eyes between rounds. Steady state is ~2 long pieces a week (roughly 2 hours), plus the Friday batch review. Buffer only builds while his editing throughput exceeds consumption or the per-piece cost drops.
- **Buffer pieces must be evergreen.** Timely pieces (news reactions, responses to a fresh interview) get slotted in when they arrive and push evergreen pieces further out. Tag every calendar row with Kind = Evergreen or Timely; build the buffer from the evergreen ones.

So the batch produces long pieces for publish dates 4-6 weeks out, not next week, and each batch starts with a buffer-depth check: count long pieces (answer articles + essays) at Reviewing, Editing, or Scheduled with publish dates ahead of today. Report the depth in weeks. Below 4, the batch produces 3 long pieces instead of 2; at or above 6, it produces 1 and spends the slack on quality.

Pipeline stages, mapped to calendar Status:
1. Idea: angle pitched (top pick + 2 alternates), awaiting JC's direction.
2. Drafting: Claude writing, gates run.
3. Reviewing: draft delivered to the repo, awaiting JC's first pass.
4. Editing: JC in rounds (2-3 sessions). Claude doesn't touch the file during this stage except when asked.
5. Proofread: after JC's final round, Claude runs a mechanical proofread (typos, punctuation, grammar; no word changes) and lists the fixes. This replaces one of JC's rounds and should be requested as a matter of routine.
6. Scheduled: final, staged in Ghost or ready to publish; announcement posts drafted against the final text.
7. Published: URL backfilled in Notion and frontmatter.

Announcement and share posts draft only against final text (Scheduled), never against a draft in Editing; quotes have to verify.

## Notion locations (Brain 2 > Projects/Areas)

- **MiT Content Calendar**, data source `3391eed6-9752-8008-9aeb-000b2baaae61`. One row per piece per day. Status flow: Idea > Drafting > Reviewing > Scheduled > Published. Fill Post URL after JC publishes.
- **MiT Content Engine** hub page `3be1eed6-9752-8136-bbc1-fb68af51f29c`, containing:
  - **Content Fodder** database, data source `8d7e8985-5d4d-4bc2-b850-a8a13f022599`
  - **MiT Competitors** page (scan list with blog links)
  - **Answer Article Prompt Backlog** page

## Batch procedure

1. **Read state**: calendar rows for the next 6 weeks, Content Fodder (New and Queued), the prompt backlog. Run the buffer-depth check and state the number.
2. **Scan for ideas.** Always scan, every batch: (a) JC's Recall knowledge base via the Recall search tools, for what he's been saving and thinking about; (b) the competitor blogs listed on the MiT Competitors page, for topics gaining traction, angles worth countering, and gaps nobody covers. Ideas don't have to come from these sources, but the scan is not optional. Bank anything promising in Content Fodder with Source set accordingly.
3. **Pick angles.** For the Thursday blog and Tuesday answer article: draft the top-pick angle in full and list two alternates in one line each. JC approves direction on substantial pieces; the drafted top pick keeps that gate fast. Wednesday and Friday posts draw from fodder first.
4. **Draft everything** per the voice skills. Editorial rules: client problem owns headlines; AI appears as tooling below the fold or not at all; critique practices, never firms or people; MiT is "AI-integrated," never "AI-first."
5. **Gate everything via bash**: em-dash grep (hard fail), banned vocabulary regex, prose colon counts, character/word counts, Honest Numbers claim audit, truncation check on post hooks (~200 chars).
6. **Update Notion**: calendar rows to Drafting with angle notes; fodder rows to Queued or Used.
7. **Deliver the packet** into the content repo (github.com/madeintandem/mit-marketing-content, local ~/dev/mit-marketing-content) per its AGENTS.md conventions, one file per piece with frontmatter, plus a short chat summary of the decisions JC needs to make. The repo is canon; chat is the review surface.

## Mid-week fodder intake

When JC sends an idea at any time, in any format: immediately add a Content Fodder row (Source: JC, Status: New, his words in the Idea and Notes fields), confirm in one line, and move on. Do not expand it into drafts unless he asks.

## Standing constraints

- No auto-publishing anywhere, ever. JC posts by hand.
- Approval gates survive speed pressure: substantial new pieces get angle approval.
- Announcement posts take a different door than the linked piece's dek; the link card already shows title and description.
- Comments on others' posts are a separate motion (reconnect, diagnose, teach) and not part of the batch.
