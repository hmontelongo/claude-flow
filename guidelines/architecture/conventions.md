# Architecture Conventions

## Architecture Layers

**Controllers are dispatchers.** Receive request, validate via Form Request, call Action or Service, return response. 10-15 lines max per method. Stick to the 7 resource methods. Custom action needed? Extract a dedicated controller: `PublicationController@store`, not `PostController@publish`.

**Models are rich.** Relationships, scopes, accessors/mutators, casts, and entity-level domain logic all live on the model. Query logic belongs as scopes, not scattered in controllers or Livewire components.

**Actions for reusable business operations.** Invocable classes in `app/Actions/` with `__invoke()` or `handle()`. Livewire components do minimal work and invoke actions. Actions are composable and reused across controllers, jobs, and APIs.

**Services for orchestration.** Coordinate multiple actions, wrap external APIs, manage stateful workflows. Consistent return types within a domain.

## Eloquent Principles

- **Always define relationships** between related models
- **Always eager load** with `with()` or `load()` — N+1 queries are unacceptable
- **Scopes for query logic**, not inline `where()` chains in controllers
- **Casts** for enums, dates, booleans, JSON — never cast manually
- **Accessors for display values** — formatting belongs on the model, not in views
- **Migrations**: indexes on foreign keys and frequently queried columns, `foreignId()->constrained()->cascadeOnDelete()`, descriptive names

## Consistent Return Types

- Create -> returns Model
- Update -> returns Model
- List -> returns Collection or Paginator
- Delete -> returns void or bool
- Validation failures throw exceptions via Form Requests

Enforce consistency within a domain — don't mix return types across similar operations.

## Form Requests

**All validation goes in Form Requests.** Never inline `$request->validate()` in controllers. In Livewire, use `#[Validate]` attributes or Form objects for complex forms.

## What NOT to Build

- **No Repository classes** unless multiple data sources exist — Eloquent IS the repository
- **No Interfaces** without 2+ implementations
- **No Services** for trivial operations — use an Action or keep it in the controller
- **No scattered queries** in controllers, Blade, or Livewire `mount()`

## Testing Philosophy

- **Feature tests as the default** — test behavior, not implementation
- **No over-mocking** — use RefreshDatabase for real DB calls, mock only external APIs you don't control
- **5-15 meaningful tests per feature**, not 50 trivial assertions
- **Mutation testing mindset**: after writing a test, mentally break the code and verify the test would catch it
- **No browser tests** — use Livewire::test() and HTTP tests for application testing, Playwright CLI for visual verification
