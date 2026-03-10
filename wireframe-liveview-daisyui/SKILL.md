---
name: wireframe-liveview-daisyui
description: Use when creating or iterating on Phoenix LiveView screens with DaisyUI and Tailwind, especially for wireframes, forms, layouts, component extraction, and light/dark-safe UI scaffolding.
---

# Wireframe LiveView with DaisyUI

Use this skill for fast, functional LiveView UI work that should stay on real Phoenix and Ash rails.

Treat DaisyUI-based prototypes as the default UI delivery path for these projects.

## Hard rules

- Use and improve shared function components before duplicating markup.
- Prefer DaisyUI components and semantic tokens for most UI structure and styling.
- Use Tailwind utilities for layout and spacing.
- Keep screens working in both light and dark themes.
- Keep prototypes on real project rails: verified routes, real Ash forms, real policies, real LiveView flows.
- Respect existing usage rules and boundary layers instead of putting domain logic directly into UI code.
- Use only `app.js` and `app.css` bundles; do not add vendor `<script>` or `<link>` tags.
- Do not write inline `<script>` blocks in HEEx. Use colocated LiveView hooks instead.
- Page templates should start with `<Layouts.app ...>` and pass the expected shared assigns such as `flash`, `current_scope`, and `current_user`.
- Use `<.link navigate={...}>` and `<.link patch={...}>`, not legacy `live_redirect` or `live_patch`.
- Use `<.form for={@form}>` plus `<.input field={@form[:field]}>`; do not pass raw changesets to templates.
- Prefer generated `form_to_*` helpers over direct `AshPhoenix.Form.for_create/for_update` when possible.
- Give important one-off elements stable DOM ids. Use descriptive `data-*` attributes where repeated elements need stable test hooks.

## Preferred workflow

1. Start with `core_components.ex` and existing components.
2. Build the smallest working screen with DaisyUI-first structure.
3. Keep URL state durable when the UI has tabs, filters, or nested CRUD modes.
4. Extract repeated or stateful UI into function components or LiveComponents before the LiveView becomes brittle.
5. Add or update tests alongside significant UI changes.

## Design defaults

- Prefer functional wireframes over bespoke styling.
- Prefer semantic classes such as `bg-base-100`, `text-base-content`, `border-base-300`, `card`, `btn`, and `alert`.
- Avoid custom CSS unless the existing component system cannot express the requirement cleanly.
- Avoid `@apply` in raw CSS.

## LiveView structure guidance

- Keep the parent LiveView as the orchestrator for routing, params, subscriptions, and cross-component refresh.
- Keep form lifecycle and local modal state inside the relevant LiveComponent when that improves clarity.
- Use streams for sizable collections when the page benefits from incremental updates.
- Prefer URL-driven state for durable UI context.

## When to load more detail

- See [examples.md](examples.md) for starter HEEx scaffolds and theme-safe examples.
- Use `phoenix-ui-tests` when you need stable selector and feature-test patterns.
- Use `working-with-ash` when the screen requires new or changed Ash resources, actions, or forms.
