---
name: test-app
description: "Write regression tests for application flows using Pest 4 browser tests. Use when asked to 'write a test for this', 'add test coverage', or 'make sure this doesn't break'. This is for PERMANENT tests that live in the codebase and run in CI. For visual verification during development (seeing what you built), use the verify-ui skill instead."
argument-hint: "[feature or flow to test]"
---

> **This skill is for permanent regression tests — written AFTER a feature is implemented and visually verified. If you're still building, use `/verify-ui` instead. Don't write a Pest test just to check your own work.**

**Project-specific:** Check CLAUDE.md for the app's URL structure, domain models, and test credentials. Adapt the examples below to match your project.

## When to Write Regression Tests

- After a feature is implemented AND visually verified
- When the user explicitly asks for test coverage
- For critical flows that must not break (login, payment, checkout)
- Before submitting a PR

## Pest 4 Browser Test Patterns

### Authentication
```php
it('can view the dashboard', function () {
    $user = User::factory()->create();
    $this->actingAs($user);

    $page = visit('/dashboard');
    $page->assertSee('Dashboard')
         ->assertNoJavascriptErrors();
});
```

### Form Submission
```php
it('can create an item', function () {
    $user = User::factory()->create();
    $this->actingAs($user);

    $page = visit('/items/create');
    $page->fill('name', 'Sample Item')
         ->fill('price', '99.99')
         ->select('category', 'general')
         ->click('Save')
         ->assertSee('Item created');

    expect(Item::where('name', 'Sample Item')->exists())->toBeTrue();
});
```

### Multi-Step Flow
```php
it('can create and publish a record', function () {
    $user = User::factory()->create();
    $this->actingAs($user);

    $page = visit('/records/create');
    $page->fill('title', 'My New Record')
         ->fill('description', 'A test record')
         ->click('Create')
         ->assertSee('Record created');

    $record = Record::where('title', 'My New Record')->first();
    expect($record)->not->toBeNull();
    expect($record->status)->toBe('draft');
});
```

### Smoke Tests (Quick Health Check)
```php
it('loads all critical pages without errors', function () {
    $this->actingAs(User::factory()->create());

    $pages = visit([
        '/dashboard',
        '/items',
        '/settings',
    ]);

    $pages->assertNoJavascriptErrors()
          ->assertNoConsoleLogs();
});
```

### Validation Tests
```php
it('shows validation errors for empty form', function () {
    $this->actingAs(User::factory()->create());

    $page = visit('/items/create');
    $page->click('Save')
         ->assertSee('The name field is required')
         ->assertSee('The price field is required');
});
```

## Test File Location
- Browser tests: `tests/Browser/`
- Feature tests: `tests/Feature/`
- Unit tests: `tests/Unit/`

## Running Tests
```bash
# Specific browser test
php artisan test tests/Browser/ItemCreationTest.php --compact

# All browser tests
php artisan test tests/Browser/ --compact

# Specific test name
php artisan test --compact --filter="can create an item"
```
