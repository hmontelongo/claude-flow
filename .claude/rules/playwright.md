# Playwright CLI Reference

Persistent reference for browser automation during development. Use this for visual verification of UI implementations.

## Installation

```bash
npm install -g @playwright/cli@latest && playwright-cli install-browser
```

## Core Commands

| Command | What it does |
|---------|-------------|
| `open [url]` | Open browser, optionally navigate to URL |
| `goto <url>` | Navigate to a URL |
| `snapshot` | Capture accessibility tree with `[ref=eNN]` refs for interaction |
| `screenshot [ref]` | Screenshot the page or a specific element |
| `close` | Close the browser |

## Finding Elements

`snapshot` returns a YAML accessibility tree with `[ref=eNN]` references. Use these refs for all interactions. Always snapshot before interacting — refs change after page updates.

## Interactions

| Command | Usage |
|---------|-------|
| `fill <ref> <text>` | Fill text into an input |
| `click <ref>` | Click an element |
| `type <text>` | Type text into the currently focused element |
| `press <key>` | Press a keyboard key (Enter, Tab, Escape, etc.) |
| `check <ref>` | Check a checkbox |
| `uncheck <ref>` | Uncheck a checkbox |
| `select <ref> <value>` | Select a dropdown option |
| `hover <ref>` | Hover over an element |

## Flux UI Login Gotcha

Flux password fields wrap `<input>` in a custom UI-FIELD element. `fill` on the ref targets the wrapper, NOT the real input. **Workaround**: click the password textbox ref to focus it, then use `type` to enter the password.

## `run-code` Is Broken

Any form of `run-code` with quotes produces TypeError. Use individual commands instead of trying to chain operations via `run-code`.

## JavaScript Evaluation

```bash
playwright-cli eval "() => document.title"
playwright-cli eval "el => el.textContent" e27
```

## State Management

```bash
playwright-cli state-save auth.json    # Save cookies/session
playwright-cli state-load auth.json    # Restore saved state
playwright-cli cookie-clear            # Clear all cookies
```

## Viewport Testing

```bash
playwright-cli resize 390 844     # Mobile
playwright-cli resize 1280 720    # Desktop
```

## Stale Process Fix

If Playwright stops responding:
```bash
ps aux | grep "mcp-chrome"
kill <PID>
```
Then retry the command.

## Typical Verification Flow

1. `open` -> navigate to the app
2. Log in (click username field, fill, click password field, type password, press Enter)
3. `goto <page-url>`
4. `screenshot` -> read the PNG to see the visual state
5. `snapshot` -> get text content via accessibility tree
6. Verify, fix issues, repeat
7. `close`

## When Vite/CSS Isn't Running

Screenshots show broken layouts, but `snapshot` still captures all text content via the accessibility tree. Use snapshots for content verification when styles aren't loading.
