---
name: phoenix-ui-tests
description: Write feature tests for both LiveView and dead/controller views using PhoenixTest.
---

# Testing LiveView and Dead Views

**PhoenixTest** provides a unified API that works identically for both LiveView and static pages. No need to distinguish between them.

## Basic Usage

```elixir
test "visiting a page", %{conn: conn} do
  conn
  |> visit("/")
  |> fill_in("Name", with: "Aragorn")
  |> click_button("Create")
  |> assert_has(".user", text: "Aragorn")
end
```

## Key Functions

**Navigation:** `visit/2`, `click_link/2`, `click_button/2`

**Forms:** `fill_in/3`, `select/3`, `check/2`, `uncheck/2`, `choose/2`, `upload/4`, `submit/1`

**Assertions:** `assert_has/2`, `assert_has/3`, `refute_has/2`, `refute_has/3`, `assert_path/2`, `refute_path/2`

**Scoping:** `within/3` - scope actions to a selector or child LiveView

## Rules

- Use `within` + semantic id (e.g. label, link text) to identify elements, not complex selectors.
- Use `timeout:` option with `assert_has/3` to wait for async LiveView operations
- In view tests, use specific selectors to check the value of the element in question. **Never** match on the entire HTML response.
- **Do not use `Phoenix.LiveViewTest` APIs in feature tests**
  - Forbidden: `live/2`, `form/2`, `render_change/1`, `render_submit/1`, `render_click/1`, `render_async/2`, `element/2`, etc.
  - These tests should start with `conn |> visit(path)` and use PhoenixTest actions/assertions end-to-end.
- **Exception**: it’s OK to use `Phoenix.LiveViewTest.render_component/2` for function-component rendering tests.
- Do **not** use `render_async/2`. Instead, wait via `assert_has(..., timeout: 10_000)` for the UI you expect.
- **If you must push messages to the LiveView process**
  - After `session = conn |> visit(path)`, you can `send(session.view.pid, msg)` (PhoenixTest sessions expose `session.view`).
- If needed for stable assertions, add id, classes or `data-*` attributes to views and target those in tests.

## Project testing expectations

- For bug fixes, add a failing test first, then make it pass.
- Use Ash generators to create test data when a generator exists. Do not use `Ash.create`.
- Do not use fixtures except vcr fixtures.
- Add security-related coverage when behavior depends on permissions or actor scope.
- Do not sleep in tests. Wait with assertions and timeouts instead.
- Review coverage for the files you touched and keep it at or above the current threshold.

## Notes

- Text matching is substring-based by default (use `exact: true` for exact matches)

## Documentation

Look up documentation for help.
To look up packages that in mix.exs are `only: :test`, set MIX_ENV=test.

- `MIX_ENV=test mix help PhoenixTest`


