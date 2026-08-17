---
name: answer-article
description: Drafting MiT's Tuesday LinkedIn answer articles and any other answer-shaped, buyer-guide content. Use this skill whenever writing or editing a LinkedIn Article for the Made In Tandem company page, whenever JC asks for an "answer article," a buyer guide, a how-to-choose piece, or a comparison framework, and whenever a piece is meant to be cited by AI search engines. Builds on top of the write-like-jc skill, which must be read first.
---

# Answer Article

Answer articles are MiT's AI-citation assets: LinkedIn Articles published from the company page every Tuesday, shaped like direct answers to questions mid-market buyers actually ask AI assistants. They exist because AI models cite structured, specific, decision-support content (Meltwater x LinkedIn, 9.5M citations, May 2026: bullets in 100% of top-cited articles, headings in 92%, named entities in 75%, hard numbers in 67%, comparison frameworks in 50%).

## Hard dependency and the zoning rule

Read `/write-like-jc` before drafting. All of its voice rules apply: banned vocabulary, no em dashes, contractions, Honest Numbers, varied rhythm, blame-nobody framing.

One deliberate exception, called the zoning rule: write-like-jc bans bullets and headers where prose belongs ("the laundromat"). In answer articles ONLY, structure is the point, not a tell. Use headings, bullets, tables, and numbered criteria freely here. Feed posts and blog essays stay prose; answer articles get structure. Same voice inside the sentences, different skeleton around them.

## Topic source

Topics come from the Answer Article Prompt Backlog page in Notion (under the MiT Content Engine hub). Pick the queued prompt, or the one that matches what surfaced in sales calls and comments that week. Mark prompts DONE with the article URL once published.

## Required structure

1. **Title**: the buyer's question, phrased how they'd ask an AI, with the year. "How to Tell If Your Company's Systems Are Ready for AI Agents (2026)". Client problem owns the title; AI tooling appears below the fold or not at all, unless the question itself is about AI.
2. **Direct answer in the first 100 words.** No throat-clearing. Answer, then earn the answer.
3. **Clear H2/H3 sections** that mirror how someone would evaluate the decision.
4. **Criteria or dimensions**, named and numbered where honest.
5. **A comparison framework or how-to-choose section.** Trade-offs stated explicitly. Describe what buyers get; never critique competitor firms by name.
6. **Named entities throughout**: Made In Tandem, the Integration Maturity Index (IMI), real systems (ERP, WMS, Snowflake), real research (METR, DORA). Entities must appear in the words, not just be implied.
7. **Verifiable numbers only.** Honest Numbers applies with full force; vendor-supplied claims get flagged as such. No invented statistics ever.
8. **FAQ**: 3 to 5 questions phrased the way buyers phrase them, each with a tight answer.
9. **Close**: one pointer to the most topically precise madeintandem.com page (never the homepage).

Length: 800 to 1,500 words. Longer means it should have been two articles.

## Companion share post

Every answer article ships with a personal share post for JC's profile: 60 to 100 words, pure prose, JC voice per write-like-jc, taking a different door than the article's opening (the LinkedIn link card already shows the title). No hashtags unless JC asks.

## Gates before delivery

Run via bash on the article and the share post: em-dash grep (zero tolerance), banned vocabulary regex, prose colon count (2 max in the share post), character/word counts, year present in title, and a claim audit (every number traceable to a source named in the text).
