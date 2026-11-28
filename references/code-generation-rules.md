# Code Generation Rules

**When to read this:** the current step involves generating client code, wrapper functions, or SDK bindings from a spec (API schema, interface definition, database schema, etc.).
**Topics:** code generation, naming conventions, type mapping, error handling, idempotency, scope discipline, terse documentation

---

## Naming

- Use the target language's native casing convention (e.g. `camelCase` for JS/TS, `snake_case` for Python, `PascalCase` for C#/Java types), even if the source spec uses a different convention.
- Never carry over spec-native prefixes/suffixes (e.g. `Get*`, `*Dto`, `I*`) unless the target language's own ecosystem conventionally uses them.
- Generated method names must be verbs or verb phrases (`createUser`, not `userCreation`).

## Type mapping

- Map spec types to the closest native type in the target language — do not default to stringly-typed fields when a structured type exists (e.g. map enums to native enums, not string constants, when the language supports it).
- Nullable/optional fields in the spec must map to the target language's own optionality mechanism (`Optional[T]`, `T?`, `T | None`), never silently dropped or defaulted.
- Preserve numeric precision constraints from the spec (e.g. an `int64` field must not be generated as a 32-bit type).

## Error handling

- Every generated method that performs I/O must surface errors through the target language's idiomatic mechanism (exceptions, `Result<T, E>`, error return values) — never swallow errors silently.
- HTTP/status-code-based errors from the spec must be mapped to distinct, typed error cases, not a single generic error type, if the target language supports discriminated errors.
- Retryable vs. non-retryable errors must be distinguishable in the generated error type or its metadata.

## Idempotency

- Generated methods for operations marked idempotent in the spec must accept and forward an idempotency key parameter if the underlying API supports one.
- Do not add client-side retry logic unless explicitly requested — that is a caller concern, not a codegen concern, unless the spec marks the operation safe-to-retry by default.

## Scope discipline (YAGNI)

- Generate exactly what the spec defines — no speculative convenience wrappers, unused overloads, or parameters for API versions the spec doesn't describe.
- Do not add configuration flags, feature toggles, or extensibility hooks for scenarios the spec doesn't require. If the spec doesn't call for it, the generated code shouldn't carry it.
- Do not scaffold caching, batching, or pagination helpers unless the spec explicitly defines that behavior — these stay caller concerns until the spec says otherwise.

## Output structure

- One generated file per logical resource/entity in the spec, not one giant file — unless the target ecosystem's convention is a single-file client (check existing project structure first).
- Generated code must include minimal inline documentation only for non-obvious constraints (units, valid ranges, side effects) — do not restate the method name in prose.
- No filler in generated comments or docstrings ("this method allows you to...", "in order to...") — state the constraint or behavior directly, one line if possible.

## Validation before finalizing

- Confirm every required field in the spec has a corresponding non-optional field in the generated type.
- Confirm every enum value in the spec is represented in the generated enum — partial enum generation is a common silent failure mode.
- Run the target language's own compiler/type-checker against the generated output before presenting it as done.