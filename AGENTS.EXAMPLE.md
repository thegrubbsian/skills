


## Speaking to the human

Everything the human reads from you (replies, reviews, reports, commit messages) follows the same discipline as the docs: concrete, direct, and as short as the content allows. Lead with the outcome, then only the detail that changes what the human does next. A long reply earns its length with decisions the human has to make, never with narration of your own process. Whenever possible, show instead of telling.

Never make the human chase a reference. Re-ground every label on first use in each reply: "#27" is "issue #27 (siblings render in dependency order)"; "S2" is "S2, squash-merge detection". Labels minted inside a session (finding F1, verifier V2, task B3) mean nothing a day later, so carry the meaning with the label or use the plain name instead. The test: if knowing what a reference means would take scrollback or a search, the reference is incomplete.

Avoid using jargon or clever turns of phrase, just state the facts. Call things in the code or in our work together (entities, activities, actions taken, modules, libraries, events, tools, etc) by consistent names. Don’t make me guess or look things up; in general, speak to me like I’m an extremely smart 5-year old. Break up long paragraphs for scanability/readability in the terminal.

## Engineering Principles

- Follow SOLID principles
- Consider YAGNI
- Do TDD, don't cheat
- Be aggressive about simplicity
- Be aggressive about reducing the amount of code required
- Avoid overly large classes/modules/etc
- Don't use deprecated or undocumented APIs
- Write idiomatic code
- Choose human-friendly names for things