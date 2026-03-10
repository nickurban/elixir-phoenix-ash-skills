---
name: working-with-ash
description: Use when creating or updating Ash resources, domains, actions, policies, relationships, migrations, code interfaces, or AshPhoenix form flows in Phoenix projects that use Ash.
---

# Working with Ash

Use this skill for implementation work inside Ash resources and domains, including schema changes and AshPhoenix form flows.

Before making Ash changes, consult the relevant Ash package `usage_rules` when available.

## Core Ash guardrails

- Stay Ash-first. Do not replace Ash patterns with Phoenix-default changesets or generic Ecto guidance unless the target code already uses Ecto directly.
- Keep resource-specific behavior in the resource module. Let domains orchestrate workflows and expose cross-resource or cross-domain interfaces.
- LiveViews should call domain-level interfaces for actions.
- Authorization belongs in policies and `Ash.can?/2`, not in duplicated UI/controller checks.
- Use dependency docs and `usage_rules` to confirm actual Ash behavior before assuming APIs or return shapes.

## Migrations

- Use `mix ash.codegen <change_name>` followed by `mix ash.migrate` for schema changes.
- Never write migrations by hand.
- Never use `mix ecto.migrate` for Ash-managed schema work.
- If a generated migration is wrong, fix the resource definition and regenerate instead of editing the migration.

## Resource modeling defaults

- Follow the configured resource section order for the project.
- Prefer `uuid_v7_primary_key`.
- Use `timestamps/1` rather than manually declaring inserted/updated timestamps.
- Set `allow_nil?: false` unless the field is intentionally nullable.
- Prefer `identities` over `custom_indexes` for uniqueness.
- Configure foreign-key behavior in `postgres.references`.

See [references/resource-patterns.md](references/resource-patterns.md) for example resource structure, relationships, and action patterns.

## Actions and policies

- Prefer atomic actions. Use `require_atomic? false` only when you have a real reason.
- Use arguments for inputs that are not persisted attributes.
- Use policy checks for authorization and validations for business rules.
- Prefer built-in policy checks before writing custom ones.
- For system-initiated actions, pass `authorize?: false` at the call site instead of hiding that behavior in permissive policies.
- Expect security-sensitive Ash and controller integration changes to be reviewed by Sobelow-class checks.

See [references/policies-and-authorization.md](references/policies-and-authorization.md) for common patterns.

## Code interfaces and forms

- Cross-domain and UI-facing calls should go through domain `define` interfaces.
- Within-domain helper calls can use resource `code_interface`.
- Prefer generated `form_to_*` helpers over direct `AshPhoenix.Form.for_create/for_update`.
- Keep the `to_form/1` result in assigns and validate/submit that stored Ash form.
- Do not introduce Ecto changesets just to power a LiveView form.

See [references/forms-and-interfaces.md](references/forms-and-interfaces.md) for example patterns.

## Related skills

- Use `phoenix-ui-tests` when adding or updating feature tests around Ash-powered UI.
- Use `planning-ash-phoenix-prototypes` when the work needs a substantial implementation plan.
- Use `wireframe-liveview-daisyui` when Ash work includes significant LiveView UI changes.

## Static analysis expectations

- Do not ignore Dialyzer issues in code you control; fix them.
- Prefer code shapes that remain explicit and analyzable by strict compile, Credo, coverage, and Dialyzer gates.
