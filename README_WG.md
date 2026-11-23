# Generic Task Manager

A type-safe task manager built with TypeScript generics. One generic `Task<T>`
class and one generic `TaskList<T>` class serve two different task types —
basic to-dos and enhanced project tasks with a due date and priority.

## Running it

```bash
npm install          # installs TypeScript locally (optional if tsc is global)
npm run build        # compiles src/*.ts -> dist/*.js
```

Then open `index.html`. Because the compiled output uses ES modules, serve the
folder rather than opening the file directly:

```bash
npx serve .          # or: python3 -m http.server
```

Use `npm run watch` to recompile on save while developing.

## File layout

| File | Responsibility |
| --- | --- |
| `src/types.ts` | All interfaces and type aliases — the shared vocabulary |
| `src/taskManager.ts` | `Task<T>`, `TaskList<T>`, storage adapter, validation, type guards. No DOM |
| `src/main.ts` | UI only: reads the form, calls the task layer, renders |
| `index.html` | Markup and form |
| `style.css` | Styles, including the visual distinction between task types |
| `tsconfig.json` | Strict compiler configuration |

## Documentation of generic type implementation choices

### 1. `TaskData` as the generic constraint

```ts
interface TaskData { title: string; description: string; }
class Task<T extends TaskData> { /* ... */ }
```

Every task type must have a title and a description, so that pair became the
constraint. The constraint is what makes the generic class useful rather than
opaque: inside `Task<T>` the compiler knows `this.data.title` is a string, so
shared behaviour can be written once. With a bare `class Task<T>`, `T` could be
`number` and that line would not compile.

### 2. Discriminated unions instead of structural guessing

Each payload carries a literal `kind` field (`'basic'` / `'enhanced'`). That
turns `BasicTaskData | EnhancedTaskData` into a discriminated union, so
TypeScript narrows automatically on `if (data.kind === 'enhanced')` — no casts
in the UI layer, and exhaustiveness is checked at compile time.

### 3. Two instantiations of one collection class

```ts
const basicTasks    = new TaskList<BasicTaskData>(...);
const enhancedTasks = new TaskList<EnhancedTaskData>(...);
```

Keeping two separately parameterised lists (instead of one
`TaskList<AnyTaskData>`) means each list is statically locked to its payload
type: `basicTasks.add({ kind: 'enhanced', ... })` is a compile error. The lists
are merged only at render time, where the `isEnhanced()` type guard narrows each
item before the enhanced-only fields are read.

### 4. Generics preserved through persistence

`serialize()` returns `SerializedTask<T>` and `Task.deserialize<U>()` returns
`Task<U>`, so the type parameter survives the round trip to `localStorage`.
Because stored JSON is untrusted at runtime, `LocalStorageAdapter<T>` takes a
type-guard function in its constructor and validates every record it reads;
records that fail are dropped rather than trusted.

### 5. Constrained generic methods

```ts
filterBy<K extends keyof T>(key: K, value: T[K]): Task<T>[]
```

`K extends keyof T` restricts the key to fields that exist on the payload, and
typing the value as `T[K]` ties it to that field — `filterBy('priority', 'urgent')`
fails to compile because `'urgent'` is not a `Priority`.

### 6. Interfaces as contracts between layers

`ITask<T>`, `ITaskList<T>` and `TaskStorage<T>` describe behaviour rather than
implementation. `TaskList` depends on `TaskStorage<T>`, not on `localStorage`
directly, so storage can be swapped or stubbed in a test without touching task
logic — which is also how the logic layer was verified headlessly.

## Error handling

A single `TaskError` class covers validation failures, storage failures and
missing DOM elements. Each UI handler catches `TaskError` and shows the message
in the alert box; anything else is re-thrown so genuine bugs stay visible in the
console. Validation lives in `validateTaskData()` rather than in HTML
attributes, so the same rules apply however a task is created (the form is
`novalidate` for exactly this reason).
