---
name: elixir-phoenix-ash
description: Use this when working on Elixir Phoenix projects with Ash
---


- Use `mix precommit` when you are done; fix any issues it reports
- If you can't find Elixir or `mix`, run `eval "$(mise activate zsh)"`
- Do not add typespecs
- HTTP client: use `:req` (`Req`); avoid `:httpoison`, `:tesla`, and `:httpc`
- Install packages with `mix igniter.install <package>`

## User questions

When a user asks a question, **never** make any changes.
**Always** research and answer the question.

### CLI tools

- Prefer `rg`/`fd`/`bat`/`jq`/`delta` over `grep`/`find`/`cat`/etc.

### Use Elixir 1.19+

- Use map update, not struct update, eg `%{user | name: "John", age: 30}`
- No regex in module constants

### General Rules

- Do not use `try`
- Only handle the args you actually receive (no 'string or atom' or 'struct or map' variants, unless you truly get both)
- Prefer pipes
- use `get_in/2`, not nested `Map.get/2`
- Prefer `if` over two-branch `case` with a default; never write `else nil` (nil is default)
- Prefer case to cond
- Prefer `item[:key]` over `Map.get(item, :key)` when equivalent; prefer `item.key` with structs
- Do not add fallback function heads/branches for unused arg or return types; keep contracts narrow so errors surface early
- When pattern matching on return, match only what the function actually returns (check docs/source)
- For functions that should not fail except in case of system error (e.g., loading data that must exist), use bang versions rather than checking return. An error in this case is a genuine exception, not a logical branch to handle.
- Be a pragmatic programmer. Prefer small changes to big ones.
- Use verified routes with sigil_p
- Ensure that code follows Elixir [naming conventions](https://hexdocs.pm/elixir/naming-conventions.html)
- Avoid [antipatterns](https://hexdocs.pm/elixir/code-anti-patterns.html)

### Reviewing code

When you are finished, review the changes.


### Architecture

Practice separation of concerns and domain-driven design.

#### Code Organization and Separation of Concerns

- View-specific query building belongs in helpers (not domain modules)
- Resource-specific operations belong in the resource module (domain can proxy, but implementation lives on the resource)
- Business workflows/orchestration belong in domain modules; LiveViews should delegate to the domain (no business logic in LiveViews)

#### Hooks

When adding hooks, **always** use LiveView Colocated Hooks unless they are reused in multiple places.

#### Function Input Contracts and Format Handling

- Be strict: handle only the formats you actually receive; remove unused variants
- When you have a struct, use direct field access for required fields; only use `Map.get/2` for truly optional fields

#### Code Interface Organization

- Cross-domain calls: domain `define` interfaces (e.g. `MyApp.Domain.list_items_by_user/1`)
- Within-domain calls: resource `code_interface` (e.g. `Item.list_by_user/1`)
- LiveViews must use domain-level interfaces (never call resource-level interfaces directly). Referring to the resource module and struct (e.g. `Accounts.User`, `%User{}`) is fine; only action calls must go through the domain.

### Plans

The first step of every bugfix should be to add a failing test.

The last steps of every plan should be:

- Ensure good tests + coverage and make them pass
- Ensure mix precommit passes
- Ensure up to date with source branch and repeat checks if needed

### Repo-local skills

Use `AGENTS.md` for repo-wide policy and constraints. Use local skills for task-specific workflow, examples, and checklists:

- Planning and implementation plans: `.codex/skills/planning-ash-phoenix-prototypes/SKILL.md`
- Ash resources, domains, policies, and migrations: `.codex/skills/working-with-ash/SKILL.md`
- LiveView UI and wireframes: `.codex/skills/wireframe-liveview-daisyui/SKILL.md`
- Phoenix feature tests for LiveView and dead views: `.codex/skills/ui-tests/SKILL.md`

### Rules for Ash

Ash project structure guidelines: [https://hexdocs.pm/ash/project-structure.html]

Check permissions using Ash.can? - do not repeat permission logic in other places.

See the [working-with-ash](.codex/skills/working-with-ash/SKILL.md) skill for comprehensive guidance on:

- Migrations
- Resources, domains, relationships, and references
- Postgres config and foreign keys
- Actions, validations, and policies
- Code interfaces
- Auth
- Testing

- Use Ash.Money for currency
- Use AshStateMachine for managing complex state
- Use Ash PubSub for publishing events

**Key reminders:**

- **Always** use `mix ash.codegen <change_name>` followed by `mix ash.migrate` for schema changes
- **Never** write migration files directly or use `mix ecto.migrate`. Use Ash not ecto directly.
- LiveViews must use domain-level Ash code interfaces, never `Ash.Query`, `Ash.read`, `Ash.create`, `Ash.update`
- Use `Ash.exists?()` for existence checks, not `Ash.read_one/2`, `Ash.get!/2`

#### Action Naming

Action names must be grammatically correct verb phrases that read naturally when prefixed with `form_to_`. Use patterns like `<verb>_<noun> or <verb>_<noun>_as_<role>`.

#### Testing

In tests:

- **Always** use an Ash Generator to create resources for tests including users. **Never** use Ash.create.
- **Never** use fixtures (except vcr)
- **Always** fix *all* test failures.
- **Always** add security-related tests (e.g. only user with permission can take action)
- Review test coverage. Make sure it didn't decrease. If it increased, update threshold in mix.exs. Never lower the threshold.
- Use [StreamData](https://hexdocs.pm/stream_data/StreamData.html) for property-based testing
- Make an appropriate tradeoff between coverage and test runtime. Don't make tests slow!
- Never sleep in tests
This message during tests usually isn't a bug:
`disconnected: ** (DBConnection.ConnectionError) client #PID<...> exited`

Run `mix coverage` to see if you have adequate test coverage for the files you are working on.

Read this for testing Ash resources: [https://hexdocs.pm/ash/test-resources.html]

When fixing a bug, add a failing test first, and then fix the bug to make it pass.

In playwright tests, if waiting for an async load, instead of sleeping, just increase the timeout on the next action or assert

#### Linting

When running dialyzer, do not ignore issues unless they are caused by code that we don't control (e.g. a library we are using).
If they are under our control, fix them!

### Agent Config

When updating AGENTS.md, skills, etc., **never** reference the app name or code.

### Security

- If you use `raw`, **always** escape any unsafe input!
- **Never reimplement security-critical functions** — use well-tested library functions instead
  - For constant-time string comparison, use `Plug.Crypto.secure_compare/2` (not `==/2` or custom implementations)
  - For cryptographic operations, use `:crypto` or well-maintained libraries
  - For password hashing, use established libraries (e.g., `bcrypt_elixir`, `argon2_elixir`)
  - Prefer standard library or dependencies for security-sensitive code
  - Custom security code is prone to bugs and timing attacks; library functions are battle-tested

### UI development

- Use the wireframe liveview skill

