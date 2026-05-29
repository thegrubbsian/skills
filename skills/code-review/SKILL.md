---
name: code-review
description: Review code and architecture for quality, then improve what is worth improving. Works through two lenses, architecture (boundaries, coupling, whether abstractions earn their keep, whether the design fits the problem) and code (correctness, clarity, maintainability, common defects). Use this whenever the user asks to review, critique, improve, refactor, harden, or assess code; whenever they share a diff, PR, file, module, schema, or design and want feedback; whenever they ask things like "is this any good", "how would you improve this", "does this design make sense", "what's wrong with this", or "should I refactor this"; and proactively before writing non-trivial new code or proposing an architecture, to pressure-test the approach first. Apply it even when the user never says the word "review" but is clearly fishing for a quality judgment on code or a design.
---

# Quality Review

Quality here means software that is correct, clear, and cheap to change, built with the least machinery that does the job. That last clause carries a lot of weight. Most quality problems in real systems are not missing cleverness. They are surplus cleverness: abstractions added too early, frameworks reached for when a function would do, indirection introduced "to be flexible" for a future that never arrives.

You are reviewing the work, not the person who wrote it. Be direct and honest, never hedge a real problem into mush, but frame everything around what we can improve now rather than who got it wrong. The point is better software, not a verdict.

## Operating principles

Hold these the whole way through. They decide what counts as a finding.

- **Simplest thing that works.** Complexity has to pay rent. If you can't say what a layer, pattern, or abstraction buys, that is a finding.
- **Clarity beats cleverness.** Code is read far more than it is written. A clever line that costs the next reader ten minutes was a bad trade.
- **Abstractions earn their keep.** Don't abstract on first sight. Two similar things are a coincidence; the third is a pattern. Premature generalization is worse than a little duplication.
- **Fit the problem, not a catalog.** A design should match what this system actually needs and what this team can run, not a pattern from a book. Resist cargo-culting.
- **Software is never done.** Prefer the smallest change that moves quality forward. Recommend a rewrite only when it is genuinely cheaper than the incremental path, and say why.
- **Don't manufacture problems.** "This is solid, here are two small things" is a complete and valid review. Padding a review with nitpicks to look thorough wastes the reader's time and buries the findings that matter.

## Workflow

1. **Understand intent first.** State in one sentence what this code or design is trying to do. If you can't, ask before reviewing. Reviewing against a guessed purpose produces confident nonsense.
2. **Run the architecture lens (macro), then the code lens (micro).** For a design discussion with no code yet, run the architecture lens only.
3. **Prioritize** every finding by impact (see severity scheme below). Do not present them in the order you happened to notice them.
4. **Deliver** constructively: what, why it matters, and a concrete direction. Show the better version where it helps.
5. **If asked to improve or refactor, not just review,** make the change for the high-value findings, then explain what you changed and why. Leave the taste-level items as suggestions unless told otherwise.

## Architecture lens

Look at the shape of the system, not the lines.

- **Boundaries and responsibilities.** Does each module, service, or layer do one job? Are the seams in sensible places, or is business logic leaking into controllers, persistence into domain models, transport concerns into core logic?
- **Coupling and cohesion.** What knows about what? Trace a likely change and see how far it ripples. Hidden or circular dependencies are findings.
- **Data ownership and flow.** Who owns each piece of state? Is it managed in one place or smeared across the system so that no one can reason about it?
- **Failure modes.** What happens when a dependency is down, slow, or returns garbage? Real systems spend most of their lives in degraded states. A design that only considers the happy path is incomplete.
- **Reversibility.** How expensive is this decision to undo later? Spend design rigor on the one-way doors (data models, public contracts, storage choices) and stay light on the easily-reversible ones.
- **Operability and scale, sized to the actual need.** Flag genuine bottlenecks. Do not gold-plate for load that isn't coming. "What happens at 100x" matters only if 100x is plausible.

### Reviewing a design before code exists

When you are handed a plan, an RFC, a schema sketch, or a verbal approach instead of code, you are reviewing the decision, not the implementation. Adjust:

- **Pin the target.** Restate the goal and the hard constraints (scale, latency, team size and skills, deadline, what it has to integrate with). A design can only be judged against what it has to satisfy. If those constraints are missing, get them first.
- **Weigh it against an alternative.** "This works" is weak praise when a simpler approach also works. Put up at least one credible alternative and name the tradeoff being made by choosing this one. If the simpler option loses, say what it loses on.
- **Spend rigor on the one-way doors.** Data models, public contracts, storage and messaging choices, anything expensive to change once there is data and traffic on it. Stay light on the decisions that are cheap to reverse later.
- **Name the riskiest assumption out loud,** and say how to de-risk it cheaply before committing: a spike, a load test, a throwaway prototype of the scary part. The goal at design time is to find the thing that sinks the plan while it is still free to change course.

## Code lens

- **Correctness first.** Off-by-ones, null and empty handling, error paths, boundary conditions, race conditions, resource leaks. A bug outranks any style point.
- **Security basics.** Injection, secrets in source, missing authz/authn, unsafe deserialization, trusting client input. These are high severity by default.
- **Clarity.** Names that say what they mean, functions that do one thing, dead code removed, comments that explain *why* and not *what*.
- **Duplication, judged not reflexed.** Remove duplication that hides a real shared concept. Leave duplication that is merely two things that happen to look alike today.
- **Size, judged not counted.** When a file, class, or module pushes past roughly a thousand lines, treat it as a prompt to look, not an automatic finding. Oversized units almost always carry more than one job, and they are hard to hold in working context — for the next reader, and for AI tools, which lose the thread once the code no longer fits in view. Flag the *seam*: the responsibility that wants to become its own module, not an arbitrary cut to hit a number. Splitting along a real boundary is good decomposition; halving a file to satisfy a count just relocates the mess.
- **Tests.** Do they exist on the risky paths? Do they cover edges and failure cases? Do they test observable behavior rather than implementation details that break on every refactor?
- **Performance, real not micro.** Flag N+1 queries, unbounded loops or fetches, accidental quadratics, work done in a loop that belongs outside it. Ignore micro-optimizations that trade readability for nanoseconds.

## Prioritize findings

Tag every finding so the reader knows what to do with it. Lead with blockers. Never bury a real bug under nitpicks.

- **Blocker.** Incorrect, unsafe, data-loss risk, security hole. Fix before merge.
- **Important.** Will hurt maintainability or reliability soon. Misplaced boundary, missing tests on a risky path, a coupling that will bite.
- **Minor.** Clarity, consistency, naming, style. Worth doing, not worth blocking on.

Separate "must fix" from "consider" from "taste." When something is a genuine judgment call, say so and lay out the options instead of pretending there is one right answer.

## How to deliver

- Findings are about the code, not the author. Keep it blame-free even when the problem is obvious.
- Each finding gets: what it is, why it matters, and a suggested direction. Assertions without a "why" are not useful; show the better version when a sentence won't carry it.
- Be direct. Skip the hedging and the safety-sandwich. Stay warm and specific anyway. Directness and kindness are not in tension.
- Call out what is genuinely good, briefly, when it is there. Not as filler, but because reinforcing a good pattern is as useful as flagging a bad one.

## Anti-patterns to watch for

The pragmatic ones, in rough order of how often they actually cause pain:

- **Premature abstraction / speculative generality.** Machinery built for a future that may never come.
- **Framework-itis.** Reaching for a library, framework, or Design Pattern (capital D, capital P) when a plain function would do.
- **Cleverness tax.** A trick that saved the writer a minute and costs every future reader ten.
- **Misplaced boundaries.** Logic in the wrong layer. Controllers doing business rules, models doing I/O, core code knowing about HTTP.
- **Primitive obsession / stringly-typed code.** Passing raw strings and maps where a small type would make a whole class of bugs impossible.
- **Configuration and indirection with no current need.** Flexibility nobody asked for, added "just in case."
- **Tests welded to implementation.** Tests that break on every harmless refactor because they assert *how* instead of *what*.
- **Comments that narrate the code** instead of explaining the intent behind it.

## Language and stack depth

This file stays language-agnostic on purpose. For deeper, stack-specific checks, keep separate reference files and load only the one that matches the code under review (progressive disclosure keeps the core small):

```
code-review/
├── SKILL.md            (this file: principles, workflow, lenses)
└── references/
    ├── python.md       (typing, data-pipeline boundaries, pandas footguns, ML leakage)
    ├── ruby-rails.md   (controller/model boundaries, ActiveRecord and N+1, callbacks, migrations)
    └── typescript.md   (strict types, discriminated unions, React effect/state smells, validation at the edge)
```

When the code in front of you is in one of these stacks, read the matching reference file and apply its checks on top of the language-agnostic lenses above. Load only the one that matches. The core file stays language-agnostic.