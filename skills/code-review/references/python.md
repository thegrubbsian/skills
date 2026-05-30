# Python (data engineering and ML)

Read this when the code under review is Python, especially data pipelines, notebooks promoted to scripts, or model training and serving code. Apply it on top of the architecture and code lenses in SKILL.md.

## Boundaries in data and ML code

The signature failure here is the 300-line script that reads, cleans, transforms, models, and writes with no seams. It runs once, nobody can change it, and nobody can test it.

- Separate **I/O** (files, APIs, databases, object storage) from **transforms** (pure functions over data) from **orchestration** (the script that wires them together). The transforms are the part with the logic worth protecting, and they are only testable if they don't reach out to the world.
- A transform that takes data in and returns data out, with no hidden reads or writes, is the unit you want. Push the side effects to the edges.
- Notebooks are fine for exploration and bad as production. If a notebook is becoming a pipeline, that is the moment to extract the transforms into a module the notebook imports.

## Typing and contracts

Python lets you pass a dict everywhere, which is exactly why data code drifts into "what keys does this thing even have."

- Type hints on public functions, non-negotiable in shared code. They are the cheapest documentation that can't go stale.
- Use `dataclass` or Pydantic for structured records instead of passing bare dicts that everyone has to remember the shape of. This is primitive obsession in Python clothing.
- For data crossing a trust boundary (config, API payloads, file formats), validate it on the way in. Pydantic earns its place here; a `TypedDict` does not enforce anything at runtime.

## Language footguns worth flagging

- **Mutable default arguments** (`def f(x, items=[])`). The default is created once and shared across calls. Use `None` and build inside.
- **Late-binding closures in loops.** Lambdas or functions defined in a loop capture the variable, not its value at definition time. Classic source of "all my callbacks use the last item."
- **Bare `except:`** swallows everything including `KeyboardInterrupt`. Catch the specific exception, or at least `except Exception`, and never silence an error you haven't handled.
- **`is` vs `==`.** `is` is identity. Use it only for `None`, `True`, `False`. Comparing values with `is` works by accident for small ints and breaks later.
- **Mutating a list while iterating it.** Iterate a copy or build a new list.
- **Money in floats.** Use `Decimal` for currency. `0.1 + 0.2` is not `0.3`.

## Pandas and dataframe footguns

- **Chained indexing / SettingWithCopyWarning.** `df[df.x > 0]['y'] = 1` may write to a copy and silently do nothing. Use `.loc[mask, 'y'] = 1`.
- **Silent dtype coercion.** Set explicit dtypes on read. A column of IDs read as float because of one missing value turns `1001` into `1001.0` and joins start failing.
- **`inplace=True`** rarely saves memory, breaks method chaining, and is being walked back across the library. Prefer reassignment.
- **`iterrows` and `apply` over rows** are slow. Vectorize. If you are writing a Python loop over a dataframe, ask whether a column operation does the same thing in a fraction of the time.

## ML-specific traps

- **Train/test leakage is the number one sin.** Fitting a scaler, encoder, or imputer on the full dataset before splitting leaks the test distribution into training. Fit on train only. A `Pipeline` makes this hard to get wrong, which is why it exists.
- **Target leakage from features.** A feature that won't exist at inference time, or that encodes the answer, inflates offline metrics and dies in production. Ask of every feature: would I have this value at the moment of prediction?
- **No baseline.** A model is only good relative to something. A trivial baseline (majority class, last value, simple heuristic) tells you whether the fancy model is earning its complexity.
- **Reproducibility.** Pin random seeds, pin dependency versions with a lockfile, and don't depend on undefined ordering for determinism. "It worked on my machine last Tuesday" is not a result.
- **Training/serving skew.** When feature computation lives in two places (a training script and a serving path), they drift. Share the code or accept that the model will rot.

## Async, when present

- A blocking call (synchronous I/O, CPU-bound work, `time.sleep`) inside an `async` function stalls the whole event loop. Keep blocking work off the loop.
- A forgotten `await` returns a coroutine object instead of the result and usually fails far from the cause.

## Testing

The pure transforms are the testable core. Test them with small, hand-built fixtures, not a 2 GB sample, and push the I/O to the edges so the logic can be tested without the world attached.

- **`pytest` is the baseline.** Plain functions with `assert`, not `unittest.TestCase` boilerplate. One behavior per test, and a name that says what broke.
- **`parametrize` over copy-paste.** `@pytest.mark.parametrize` turns a dozen near-identical cases into one table, and makes the edge cases you cover (and the ones you don't) visible at a glance.
- **Fixtures, with an eye on scope.** Function-scoped by default. A `scope="module"` or `scope="session"` fixture holding mutable state leaks between tests and creates order-dependence — the pytest form of the shared-mutable-fixture bug. Use `tmp_path` / `tmp_path_factory` for filesystem work instead of writing into the repo.
- **Mock at the I/O boundary, not the transform.** `monkeypatch` for env and attributes; `responses` or `respx` for HTTP; a small in-memory double for the database. If you're patching the function under test, you're testing the patch. The reason to separate transforms from I/O is so you barely have to mock at all.
- **Property-based testing (Hypothesis)** fits data transforms: assert invariants ("output row count never exceeds input," "no nulls introduced," "round-trips losslessly") rather than enumerating cases. It finds the empty frame and the single-row frame you didn't think to write.
- **Freeze time, seed randomness.** `freezegun` or an injected clock for date-dependent logic; an explicit seed for anything stochastic. A test whose result depends on the day it runs is already broken.
- **For ML, test the boundaries that rot.** A test that fits a transformer on train and asserts it never touched test data catches leakage; a test that runs one input through both the training and serving feature code catches skew. These are the bugs that don't show up until production.
- **Mark the slow and integration tests** (`@pytest.mark.slow`, `@pytest.mark.integration`) so the fast suite stays runnable on every change. A suite nobody runs because it takes ten minutes protects nothing.
- **Don't test pandas. Test your logic.** Asserting that `groupby` groups is testing the library; assert the business rule your transform encodes.

## Performance

- Vectorize before you optimize anything else.
- Watch memory on large frames: right-size dtypes, read in chunks, and don't materialize a whole dataset when a generator would stream it.
- ORM N+1 applies here too. SQLAlchemy lazy-loads relationships by default; use `joinedload` or `selectinload` when you know you'll touch them.
