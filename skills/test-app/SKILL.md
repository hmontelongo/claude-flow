---
name: test-app
description: "Write meaningful regression tests for application features using Pest feature tests and Livewire::test(). Use when the user says 'write tests', 'add tests', 'test this', 'add coverage', or 'make sure this doesn't break'. Writes feature tests only -- no browser tests, no screenshot tests. NOT for visual verification -- use /verify-ui for that."
argument-hint: "[feature or flow to test]"
---

> **This skill writes permanent regression tests -- written AFTER a feature is implemented and visually verified. If you're still building, use `/verify-ui` instead.**

**Project-specific:** Check CLAUDE.md for the app's URL structure, domain models, and test credentials.

## When to Write Tests

- After a feature is implemented AND visually verified
- When the user explicitly asks for test coverage
- For critical flows that must not break
- Before submitting a PR

## Test Patterns

### HTTP Tests (Routes, Controllers, Authorization)

```php
it('can view the dashboard', function () {
    $user = User::factory()->create();

    $this->actingAs($user)
        ->get('/dashboard')
        ->assertOk()
        ->assertSee('Dashboard');
});

it('requires authentication', function () {
    $this->get('/dashboard')
        ->assertRedirect('/login');
});

it('can create an item', function () {
    $user = User::factory()->create();

    $this->actingAs($user)
        ->post('/items', [
            'name' => 'Sample Item',
            'price' => 9999,
        ])
        ->assertRedirect();

    $this->assertDatabaseHas('items', [
        'name' => 'Sample Item',
        'price' => 9999,
    ]);
});
```

### Livewire::test() (Components, Forms, Interactions)

```php
it('can search items', function () {
    $user = User::factory()->create();
    Item::factory()->for($user)->create(['name' => 'Findable']);
    Item::factory()->for($user)->create(['name' => 'Hidden']);

    Livewire::actingAs($user)
        ->test(ItemList::class)
        ->set('search', 'Find')
        ->assertSee('Findable')
        ->assertDontSee('Hidden');
});

it('can create an item via form', function () {
    $user = User::factory()->create();

    Livewire::actingAs($user)
        ->test(CreateItem::class)
        ->set('form.name', 'New Item')
        ->set('form.price', 1500)
        ->call('save')
        ->assertDispatched('item-created');

    $this->assertDatabaseHas('items', ['name' => 'New Item']);
});

it('validates required fields', function () {
    $user = User::factory()->create();

    Livewire::actingAs($user)
        ->test(CreateItem::class)
        ->call('save')
        ->assertHasErrors(['form.name', 'form.price']);
});

it('can delete an item with confirmation', function () {
    $user = User::factory()->create();
    $item = Item::factory()->for($user)->create();

    Livewire::actingAs($user)
        ->test(ItemList::class)
        ->call('delete', $item->id)
        ->assertDispatched('item-deleted');

    $this->assertDatabaseMissing('items', ['id' => $item->id]);
});
```

### Action/Service Tests

```php
it('calculates order total correctly', function () {
    $order = Order::factory()
        ->has(OrderItem::factory()->count(3)->state(['price' => 1000, 'quantity' => 2]))
        ->create();

    $total = app(CalculateOrderTotal::class)($order);

    expect($total)->toBe(6000);
});
```

## Quality Rules

### The Mutation Testing Step

After writing each test, mentally break the code it covers:
1. What if you delete the line that saves to the database? Does a test fail?
2. What if you swap a condition from `>` to `<`? Does a test catch it?
3. What if you remove the authorization check? Does a test notice?

If no test fails, the test suite is insufficient. Add a test that would catch the mutation.

### What Makes a Good Test

- **Tests behavior, not implementation** — test what the user experiences, not internal method calls
- **Uses real database** — RefreshDatabase trait, factories for data setup
- **Mocks only external APIs** — Stripe, SendGrid, etc. Never mock Eloquent or internal services
- **Has meaningful assertions** — `assertDatabaseHas` > `assertOk`, `assertSee('specific text')` > `assertStatus(200)`
- **One concept per test** — each `it()` block tests one behavior

### What NOT to Do

- No `visit()` or `assertNoJavascriptErrors()` — those are browser test patterns
- No mocking Eloquent models or internal services
- No trivial tests (bare `assertOk()` with no meaningful assertion)
- No testing framework features (Eloquent relationships, built-in validation rules)

## Test File Location

- Feature tests: `tests/Feature/`
- Livewire tests: `tests/Feature/Livewire/`
- Unit tests: `tests/Unit/` (rare — only for pure logic with no dependencies)

## Running Tests

```bash
# All tests
vendor/bin/pest

# Specific file
vendor/bin/pest tests/Feature/ItemTest.php

# Specific test by name
vendor/bin/pest --filter="can create an item"

# With coverage
vendor/bin/pest --coverage --min=80
```

## Target

5-15 meaningful tests per feature. Each test should catch a real bug if it fails. Quality over quantity.
