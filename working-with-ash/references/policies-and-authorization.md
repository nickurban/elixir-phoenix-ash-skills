# Policies and Authorization

## Defaults

- Prefer built-in policy checks such as `actor_present/0`, `relates_to_actor_via/1`, and `actor_attribute_equals/2`.
- Put authorization in policies.
- Put business rules in validations.
- Use `Ash.can?/2` when the caller needs to ask whether an action is permitted.

## Ownership pattern

```elixir
policies do
  policy action_type(:create) do
    authorize_if actor_present()
  end

  policy action_type(:read) do
    authorize_if relates_to_actor_via(:owner)
  end
end
```

## Nested ownership

```elixir
policy action_type(:read) do
  authorize_if relates_to_actor_via([:parent, :owner])
end
```

## System actions

For true system work, keep authorization explicit at the call site:

```elixir
MyApp.Domain.create_thing(params, authorize?: false)
```

Avoid policy rules like `authorize_if always()` just to support system callers.
