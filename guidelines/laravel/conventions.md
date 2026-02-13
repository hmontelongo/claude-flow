# Laravel Conventions

## Architecture Layers

**Controllers are dispatchers.** Receive request → validate via Form Request → call Action or Service → return response. 10-15 lines max per method. Stick to the 7 resource methods. Custom action needed? Extract a dedicated controller: `PublicationController@store`, not `PostController@publish`.

**Models are rich.** Relationships, scopes, accessors/mutators, casts, and entity-level domain logic all live on the model. Query logic belongs as scopes, not scattered in controllers or Livewire components.

**Actions for reusable business operations.** Invocable classes in `app/Actions/` with `__invoke()` or `handle()`. Livewire components do minimal work and invoke actions. Actions are composable and reused across controllers, jobs, and APIs.

**Services for orchestration.** Coordinate multiple actions, wrap external APIs, manage stateful workflows. Consistent return types within a domain.

## Eloquent Conventions

- **Always define relationships** between related models.
- **Always eager load** with `with()` or `load()`. N+1 queries are unacceptable. Watch for `$items->map(fn($i) => $i->author->name)` without eager loading — CC tends to write this.
- **Scopes for query logic**, not inline `where()` chains in controllers.
- **`$casts`** for enums, dates, booleans, JSON. Never cast manually.
- **Accessors for display values.** `$item->formatted_price`, not `number_format()` in Blade or views.
- **Migrations**: indexes on foreign keys and frequently queried columns, `foreignId()->constrained()->cascadeOnDelete()`, descriptive names.

## Consistent Return Types

- Create → returns `Model`
- Update → returns `Model`
- List → returns `Collection` or `Paginator`
- Delete → returns `void` or `bool`
- Validation failures throw exceptions via Form Requests

CC frequently mixes return types across similar operations — enforce consistency within a domain.

## Form Requests

**All validation goes in Form Requests.** Never inline `$request->validate()` in controllers. In Livewire, use `#[Validate]` attributes or Form objects for complex forms.

## What NOT to Do

- **No Repository classes** unless multiple data sources exist. Eloquent IS the repository.
- **No Interfaces** without 2+ implementations.
- **No Services** for 5-line operations — use an Action or keep it in the controller.
- **No scattered queries** in controllers, Blade, or Livewire `mount()`.

## Testing Philosophy

**Test what matters**: critical user flows, business logic in Actions/Services, authorization (Policies), validation (Form Requests), regressions.

**Do NOT test**: Eloquent built-ins, framework features, trivial getters, bare 200-status assertions that prove nothing.

**When asked to "add tests"**: 5-15 meaningful tests per feature, not 50 trivial ones. Each test should catch a real bug if it fails.

**Pest conventions**: feature tests as default, `describe()` blocks, sentence-style names (`it('can create an item')`), factories for data, `actingAs()` for auth, `assertDatabaseHas()` for writes.
