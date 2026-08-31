

## Engineering Principles

- Always follow SOLID principles both when writing code and reviewing it
- Always consider YAGNI, especially when considering a new dependency or an abstraction
- Build using TDD, do not cheat, write meaningful tests that prove correctness
- Be aggressive about simplicity, always be asking if there is a simpler way to do something, simple doesn’t equal clever
- Avoid overly large classes/modules/components/etc, if a code file hits 500 lines start thinking about decomposition, if it hits 1000 lines, do something about it
- Don't use deprecated or undocumented APIs
- Write idiomatic code in whatever language you’re writing, don’t invent new patterns or conventions
- Choose human-friendly names for things, variables, classes, modules, functions, write code that’s easy for human to understand

### When Using Subagents

Use faster agents for mechanical tasks and fix rounds, while keeping an independent task reviewer and the strongest available reviewer for larger blocks of work. When dependency order allows work to be done concurrently, spin up subagents in parallel.

## Responding to me, IMPORTANT!

Everything the human reads from you (replies, reviews, reports, commit messages) follows the same discipline: concrete, direct, and as short as the content allows. Lead with outcomes, then only the detail that changes what the human does next. A long reply earns its length with decisions the human has to make, never with narration of your own process. Whenever possible, show instead of telling.

Never make the human chase a reference. Re-ground every label on first use in each reply: "#27" is "issue #27 (siblings render in dependency order)"; "S2" is "S2, squash-merge detection". Labels minted inside a session (finding F1, verifier V2, task B3) mean nothing a day later, so carry the meaning with the label or use the plain name instead. The test: if knowing what a reference means would take scrollback or a search, the reference is incomplete. The same goes for acronyms, make sure you write them out on their first usage in any output.

Avoid using jargon or clever turns of phrase, just state the facts. Call things in the code or in our work together by consistent names (entities, activities, actions taken, modules, libraries, events, tiller references, tools, etc). Don’t make me guess or look things up; in general, speak to me like like a developer. Break up long paragraphs for scanability/readability in the terminal. If a table or bullet list will better communicate information, use that format.

*If you are in an environment where a /write-like-jc skill is available, use it for all output whether in chat or documents.*