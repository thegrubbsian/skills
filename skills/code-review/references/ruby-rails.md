# Ruby on Rails

Read this when the code under review is Rails. Apply it on top of the architecture and code lenses in SKILL.md.

## Where Rails logic goes to die

Rails makes two mistakes easy: the fat controller and the fat model. Business logic ends up in whichever one the author touched last.

- **Controllers** are about HTTP: params in, response out, auth checks, and delegation. A controller doing multi-step business logic is a smell.
- **Models** are about data and the invariants that protect it. A `User` model with eighty methods has become a junk drawer. Persistence and validation belong here; "send the welcome sequence and charge the card and notify Slack" does not.
- **Put real operations in plain objects** (service objects, interactors, POROs). A multi-step workflow with its own name deserves its own class. But do not over-rotate: a one-line operation does not need a service object and a factory and an interface. Pragmatism cuts both ways.
- **Concerns** should share genuine behavior, not hide size. A concern extracted only to make a 600-line model look like a 300-line model plus a 300-line concern fixed nothing.

## ActiveRecord, where the bodies are buried

- **N+1 queries.** The canonical Rails performance bug. Loading a collection and then touching an association per item fires one query per row. Use `includes`, `preload`, or `eager_load`. Watch for it hiding in views and serializers, not just controllers. The `bullet` gem catches these.
- **Missing indexes.** Foreign keys and any column you filter or sort on need an index. Rails does not add them for you by default in older versions, and an unindexed FK on a large table is a slow query waiting to happen.
- **Validations without database constraints.** A `validates :email, uniqueness: true` does not prevent duplicates under concurrency. Two requests pass the validation at the same time, both insert. If the invariant matters, back it with a unique index. The model validation is for nice error messages; the database constraint is what actually holds the line.
- **`default_scope` is a trap.** It leaks into every query and association, surprises everyone, and is painful to opt out of. Prefer explicit named scopes.
- **Callbacks that do too much.** An `after_create` that sends email, an `after_save` that calls an external API. Callbacks fire on every save including ones you didn't expect (tests, bulk updates, admin edits), they make models impossible to reason about, and they couple persistence to side effects. Prefer explicit orchestration in a service object over a chain of callbacks.

## Migrations

- Keep migrations reversible, or define `up` and `down` explicitly when Rails can't infer the rollback.
- Be careful mixing schema changes and data backfills in one migration on a large table. The schema change may lock; the backfill may run for an hour.
- On big tables, add indexes concurrently (`disable_ddl_transaction!` plus `add_index ..., algorithm: :concurrently`) so you don't take a write lock in production.

## Security

- **SQL injection** through string interpolation in `where`. `where("name = '#{params[:q]}'")` is a hole. Use `where("name = ?", params[:q])` or the hash form.
- **Mass assignment.** Strong params exist for a reason. Permit explicitly; never `permit!` on user input.
- **Authorization is not authentication.** Devise logs them in. It does not decide what they're allowed to touch. Every action that exposes or mutates someone's data needs an explicit ownership or policy check (Pundit, CanCanCan, or hand-rolled). Missing authorization is the most common serious Rails bug.
- **XSS via `html_safe` and `raw`.** Both bypass escaping. Any user-influenced string passed through them is a vector.

## Background jobs

- Jobs get retried. They must be **idempotent**, or a retry double-charges, double-sends, double-creates.
- Pass **record IDs, not whole objects**, as job arguments. Serialized objects bloat the queue and go stale between enqueue and run. Refetch inside the job.
- Keep jobs small and single-purpose. A job that does five things fails partway and leaves you with a mess on retry.

## Testing

- Test behavior through the public interface. Request specs exercise the real stack; heavy controller specs that stub everything test your mocks.
- Keep factories lean. A factory that builds a whole object graph for every test makes the suite slow and brittle.
- Don't over-mock. Mocking the thing under test means you're testing nothing.

## Performance

- N+1 again, because it is that common.
- Use `pluck` or `select` to pull only the columns you need instead of hydrating full objects.
- Counter caches beat `COUNT` queries on hot paths.
- Don't load a huge collection into memory to iterate it. `find_each` batches.

## Rails smells, quick list

- God models, callback chains, business logic in views and helpers, `default_scope`, validations with no DB constraint behind them, and the service object that should have just been a method.
