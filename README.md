# bun-react-effect-example

![Bun + React + Effect Example](src/promo.png)

I wanted to see what end-to-end type safety actually looks like in a small app if you commit to Effect all the way through. Started with `bun init` React + shadcn/ui, replaced every try/catch and runtime check with Effect's typed error channels, tagged errors, and Schema validation. Zero `any` types, zero type assertions, zero runtime type errors.

The `bun init` React + shadcn/ui starter, made type-safe end-to-end with Effect TypeScript.

## Stack

| Layer | Tech |
| --- | --- |
| Runtime | Bun |
| Frontend | React 19, Tailwind CSS v4, shadcn/ui |
| Backend | `Bun.serve` with Effect-based handlers |
| Validation | Effect Schema (runtime type checking) |
| Build | `Bun.build` with Effect error handling |

## Commands

```bash
bun install    # install dependencies
bun dev        # development server with HMR
bun start      # production server
bun build      # production build (supports CLI args)
```

## Structure

```
src/
├── index.ts        # server entry with typed Effect handlers
├── App.tsx         # React root
├── APITester.tsx   # API test UI with Schema validation
├── frontend.tsx    # React entry with HMR
├── lib/
│   ├── errors.ts   # tagged errors (ValidationError, HttpError, etc.)
│   └── utils.ts    # utilities (cn)
└── components/ui/  # shadcn/ui components
build.ts            # production build with Effect + CLI args
```

## Type safety patterns

### Tagged errors

```typescript
const getHelloByName = (name: string): Effect.Effect<HelloByNameResponse, ValidationError> =>
  Effect.gen(function* () {
    if (!name.trim()) {
      return yield* new ValidationError({
        message: "Name parameter is required",
        cause: { endpoint: "/api/hello/:name", value: name }
      });
    }
    return { message: `Hello, ${name}!` };
  });
```

Error type is enforced at compile time. Handlers must `Effect.catchTag("ValidationError", ...)` to recover.

### Frontend validation

- API responses validated with `Schema.Struct` and `Schema.Union`
- `Effect.catchTag` for type-safe error recovery by tag
- Form inputs validated with `Schema.decodeUnknownEither` before API calls

### Build system

- Effect-based build script with tagged error types (`CleanError`, `BuildError`)
- CLI argument parsing with automatic type coercion
- Resource cleanup via `Effect.acquireRelease`

Run `bun build --help` for all CLI options.

## License

MIT
