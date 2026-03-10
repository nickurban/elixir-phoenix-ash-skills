# Codex Skills for Elixir Phoenix Ash Prototypes

This repository contains opinionated Codex skills for Elixir/Phoenix projects built around Ash, AshAuthentication, AshPhoenix forms, PhoenixTest, DaisyUI, and related prototype workflows.


## Included skills

- `elixir-phoenix-ash`: umbrella skill for the opinionated stack and shared conventions
- `working-with-ash`: Ash resources, domains, policies, actions, migrations, and forms
- `wireframe-liveview-daisyui`: LiveView UI scaffolding with DaisyUI and Tailwind
- `phoenix-ui-tests`: PhoenixTest feature tests for LiveView and dead views
- `planning-ash-phoenix-prototypes`: implementation planning for Ash/Phoenix/LiveView work

## Usage model

- These skills prefer repo conventions such as `usage_rules`, `boundary`, `mix precommit`, generated `form_to_*` helpers, and `mix igniter.install`.
- When those conventions are absent, the skills are written to degrade gracefully to standard Phoenix/Ash workflows rather than fail.
- Each skill lives in its own folder with a required `SKILL.md` and optional `agents/` or `references/` files.

## Repository layout

```text
elixir-phoenix-ash/
phoenix-ui-tests/
planning-ash-phoenix-prototypes/
wireframe-liveview-daisyui/
working-with-ash/
```

Open the relevant `SKILL.md` in each directory for the actual instructions.

## Author

Copyright [Nick Urban](https://github.com/nickurban)
