---
name: test-app
description: "Write regression tests for application flows using Pest 4 browser tests. Use when asked to 'write a test for this', 'add test coverage', or 'make sure this doesn't break'. This is for PERMANENT tests that live in the codebase and run in CI. For visual verification during development (seeing what you built), use the verify-ui skill instead."
---

## Important Distinction

**This skill is for regression tests** — permanent, repeatable tests that live in `tests/Browser/` and run in CI to prevent regressions.

**This is NOT for visual verification during development.** If you just built a page and need to see if it works, use the `verify-ui` skill with Playwright CLI. Don't write a Pest test just to check your own work — that's what the browser is for.

## When to Write Regression Tests

- After a feature is implemented AND visually verified
- When the user explicitly asks for test coverage
- For critical flows that must not break (login, payment, sharing)
- Before submitting a PR

## Pest 4 Browser Test Patterns

### Authentication
```php
it('can view the dashboard', function () {
    $user = User::factory()->create();
    $this->actingAs($user);

    $page = visit('/agent/dashboard');
    $page->assertSee('Dashboard')
         ->assertNoJavascriptErrors();
});
```

### Form Submission
```php
it('can create a property', function () {
    $user = User::factory()->create();
    $this->actingAs($user);

    $page = visit('/agent/properties/create');
    $page->fill('title', 'Casa en Zapopan')
         ->fill('price', '2500000')
         ->select('property_type', 'casa')
         ->click('Guardar')
         ->assertSee('Propiedad creada');

    expect(Property::where('title', 'Casa en Zapopan')->exists())->toBeTrue();
});
```

### Multi-Step Flow
```php
it('can create and share a collection', function () {
    $user = User::factory()->create();
    $client = Client::factory()->for($user)->create();
    $this->actingAs($user);

    $page = visit('/agent/collections/create');
    $page->fill('name', 'Opciones para Juan')
         ->select('client_id', $client->id)
         ->click('Crear')
         ->assertSee('Colección creada');

    $collection = Collection::where('name', 'Opciones para Juan')->first();
    expect($collection)->not->toBeNull();
    expect($collection->share_token)->not->toBeNull();
});
```

### Smoke Tests (Quick Health Check)
```php
it('loads all critical pages without errors', function () {
    $this->actingAs(User::factory()->create());

    $pages = visit([
        '/agent/dashboard',
        '/agent/properties',
        '/agent/collections',
        '/agent/clients',
    ]);

    $pages->assertNoJavascriptErrors()
          ->assertNoConsoleLogs();
});
```

### Validation Tests
```php
it('shows validation errors for empty property form', function () {
    $this->actingAs(User::factory()->create());

    $page = visit('/agent/properties/create');
    $page->click('Guardar')
         ->assertSee('El título es obligatorio')
         ->assertSee('El precio es obligatorio');
});
```

## Test File Location
- Browser tests: `tests/Browser/`
- Feature tests: `tests/Feature/`
- Unit tests: `tests/Unit/`

## Running Tests
```bash
# Specific browser test
php artisan test tests/Browser/PropertyCreationTest.php --compact

# All browser tests
php artisan test tests/Browser/ --compact

# Specific test name
php artisan test --compact --filter="can create a property"
```
