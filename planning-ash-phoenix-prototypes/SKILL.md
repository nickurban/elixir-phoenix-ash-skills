---
name: planning-ash-phoenix-prototypes
description: Use when creating implementation plans for Ash, Phoenix, and LiveView work. Produces concise, durable plans that stay Ash-first, identify dependencies early, and map requirements to concrete execution steps.
---

# Planning Ash Phoenix Prototypes

Use this skill when the user wants a plan rather than immediate implementation.

## Planning goals

- Produce a plan that can drive real implementation work.
- Stay Ash-first when the system is built on Ash resources and domains.
- Respect existing `usage_rules` and boundary conventions when the repo defines them.
- Keep the plan concise; do not repeat detail already captured in other skills.
- Map each explicit requirement, review finding, and notable risk to a task, decision, or explicit deferment.

## Before drafting the plan

Identify the requirements and search for existing tools that simplify the implementation:

- relevant Ash extensions
- project dependencies already in use
- well-maintained Elixir libraries when no Ash extension fits

Ask the user before locking in a choice only when it introduces a new dependency, materially changes architecture, or creates a meaningful trade-off they should choose.

## What the plan should include

- the intended resource, domain, LiveView, and test shape
- the key decisions and why they were chosen
- the execution order, preferably in independently testable vertical slices
- the verification path, including tests and `mix precommit`

## Durable plan artifacts

If the repo has an established durable planning artifact or plan-file convention, use it for substantial work. If not, keep the plan in the response. Do not invent unnecessary planning ceremony for obvious small changes.

## Ash-first planning rules

- Plan real Ash resources, actions, forms, and policies rather than throwaway Phoenix-default stand-ins.
- Put authorization work in policies, not action validations.
- Let Ash and extensions handle defaults they already own.
- Route work through the intended boundaries instead of planning cross-layer shortcuts.
- Prefer a vertical slice first when the shape is uncertain.

## Final plan checks

Before presenting the plan, confirm:

- the plan is the simplest approach that fits the codebase
- dependency choices are explicit
- no requirement was silently dropped
- the plan references specialist skills instead of duplicating them
- the final steps include tests, coverage review when relevant, and `mix precommit`

## Related skills

- Use `working-with-ash` for detailed resource, policy, migration, and form guidance.
- Use `wireframe-liveview-daisyui` for the default DaisyUI prototype path, LiveView UI structure, and component decisions.
- Use `phoenix-ui-tests` for feature-test strategy and PhoenixTest usage.
