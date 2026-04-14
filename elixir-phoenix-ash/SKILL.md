---
name: elixir-phoenix-ash
description: Use when working in Elixir Phoenix projects built around Ash, AshAuthentication, AshPhoenix forms, and PhoenixTest. This is the umbrella skill for shared project conventions and routing to the more specific Ash, UI, testing, and planning skills.
license: MIT
---

# Elixir Phoenix Ash

Use this as the umbrella skill for opinionated Phoenix prototype projects that consistently use Phoenix, Ash, AshAuthentication, AshPhoenix forms, PhoenixTest, and TDD.

These projects often define shared `usage_rules`, `boundary` modules, `mix precommit`, and local shell tooling. Use those conventions when they are present in the repo, and degrade gracefully to the closest standard Phoenix/Ash workflow when they are not.

## Core defaults

- Use `mix precommit` when the repo defines it; otherwise run the narrowest available verification commands for the files you changed, then note what full-project checks were unavailable.
- If `mix precommit` exists, expect it to gate on compilation warnings, dependency hygiene, formatting, security checks, strict linting, coverage, and Dialyzer.
- If `mix` or Elixir are unavailable in the shell and the environment uses `mise`, run `eval "$(mise activate zsh)"`.
- Do not add typespecs unless the user explicitly asks for them.
- Use `Req` / `:req` for HTTP; avoid `:httpoison`, `:tesla`, and `:httpc`.
- Install packages with `mix igniter.install <package>` when that workflow is available; otherwise use the repo's normal dependency-management path.
- Prefer `rg`, `fd`, `bat`, `jq`, and `delta` over slower defaults.
- If expected project tooling or conventions are missing, tell the user briefly before adapting. Examples: no `mix precommit`, no `usage_rules` tasks, no `mix igniter.install`, or missing boundary/helper modules the normal workflow assumes.

## Question-only requests

When the user asks a question rather than requesting implementation, do not make code changes. Inspect the codebase, research the answer, and respond directly.

## Project guidance sources

- Thoroughly consult dependency `usage_rules` before making changes in the areas they cover.
- When looking up dependency or Elixir module docs, prefer `mix usage_rules.docs` if the task exists; otherwise use the package docs or `mix help`.
- When searching package documentation, prefer `mix usage_rules.search_docs` if the task exists; otherwise use the best available local or official docs.
- Use `mix help <task>` before running unfamiliar mix tasks.
- If those expected documentation helpers are absent, say so briefly rather than implying they exist.

## Elixir and Phoenix guardrails

- Prefer small, pragmatic changes over broad rewrites.
- Treat compiler warnings as real failures and fix them, since projects commonly compile with warnings-as-errors in precommit.
- Do not use `try` unless you are handling a real exception boundary that cannot be expressed more directly.
- Handle only the inputs and return shapes the code actually receives; do not add speculative fallback branches.
- Prefer `case` over `cond`.
- Prefer `if` over a two-branch `case` with a default branch.
- Prefer `get_in/2` over nested `Map.get/2`.
- Prefer `item[:key]` for maps and `item.key` for structs when the data shape is known.
- Use verified routes with sigil `~p`.
- Keep business workflows in domains and keep LiveViews focused on orchestration and UI state.
- Use colocated LiveView hooks unless the hook is intentionally reused.
- Strings and atoms are not the same! They are distinct as map keys. Make sure to pass or accept only the ones that you need! 

## Ash-first architecture

- Stay Ash-first when the feature is built on Ash resources and domains; do not drift into Phoenix-default or Ecto-first patterns unless the code already does so for a good reason.
- LiveViews should call domain-level interfaces for actions.
- Authorization belongs in Ash policies, AshAuth flows, and `Ash.can?/2`, not repeated ad hoc in LiveViews or controllers.
- Respect established boundary modules and usage rules when they exist rather than bypassing them from UI or orchestration code.

## Testing defaults

- For bug fixes, add a failing test first.
- Keep tests fast; do not sleep in tests.
- Fix all failures introduced or exposed by the change.

## Dependency and security hygiene

- Keep dependency changes intentional and minimal; expect audits and unused-dependency checks in precommit.
- Treat security-sensitive changes as likely Sobelow targets.

## Skill routing

Use the narrower skills when the task fits them:

- `working-with-ash`: resources, domains, actions, policies, migrations, code interfaces, Ash forms.
- `phoenix-ui-tests`: feature tests with PhoenixTest for LiveView and dead views.
- `wireframe-liveview-daisyui`: the default path for LiveView UI scaffolding, HEEx structure, DaisyUI/Tailwind usage, and component extraction.
- `planning-ash-phoenix-prototypes`: implementation plans for Ash/Phoenix/LiveView work.

Load those skills instead of repeating their detailed instructions here.
