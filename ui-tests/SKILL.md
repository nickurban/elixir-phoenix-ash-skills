---
name: phoenix-ui-tests
description: Use when writing or updating Phoenix feature tests with PhoenixTest for LiveView and dead views, including Ash-powered forms and permission-sensitive UI flows.
---

# Phoenix UI Tests

Use this skill for end-to-end style Phoenix feature tests built with PhoenixTest.

Check relevant `usage_rules` before introducing new testing patterns.

## Default approach

- Start tests with `conn |> visit(path)`.
- Use PhoenixTest actions and assertions end-to-end for both LiveView and dead/controller views.
- For bug fixes, add a failing test first.
- Keep tests fast; never sleep in tests.

## Core PhoenixTest APIs

- Navigation: `visit/2`, `click_link/2`, `click_button/2`
- Forms: `fill_in/3`, `select/3`, `check/2`, `uncheck/2`, `choose/2`, `upload/4`, `submit/1`
- Assertions: `assert_has/2`, `assert_has/3`, `refute_has/2`, `refute_has/3`, `assert_path/2`, `refute_path/2`
- Scoping: `within/3`

## Rules

- Do not use `Phoenix.LiveViewTest` interaction APIs in feature tests.
- Use `within/3` and stable semantic identifiers rather than brittle selectors.
- If async UI work needs time, wait with `assert_has(..., timeout: ...)`.
- Assert on specific elements, not the entire HTML response.
- If the view needs better test hooks, add stable DOM ids or descriptive `data-*` attributes.
- Confirm PhoenixTest APIs and test helper behavior with local docs when uncertain.

It is acceptable to use `Phoenix.LiveViewTest.render_component/2` for isolated function-component rendering tests.

## Ash-aware testing

- Use Ash generators to create test data when available. Do not use `Ash.create`.
- Exercise Ash-generated forms through the rendered UI.
- Add permission coverage when behavior depends on actor scope or authorization.

## Documentation

If you need API help, inspect PhoenixTest docs locally. Packages that are test-only may require `MIX_ENV=test`.

- `MIX_ENV=test mix help PhoenixTest`
- `mix usage_rules.docs PhoenixTest` and `mix usage_rules.search_docs PhoenixTest` when available

## Related skills

- Use `working-with-ash` for resource and form architecture behind the UI.
- Use `wireframe-liveview-daisyui` when testability depends on UI structure or selectors.
