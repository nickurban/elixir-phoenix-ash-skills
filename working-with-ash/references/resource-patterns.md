# Resource Patterns

## Baseline resource shape

```elixir
defmodule MyApp.Domain.Resource do
  use Ash.Resource,
    otp_app: :my_app,
    domain: MyApp.Domain,
    data_layer: AshPostgres.DataLayer,
    authorizers: [Ash.Policy.Authorizer]

  postgres do
    table "resources"
    repo MyApp.Repo

    references do
      reference :owner, on_delete: :restrict
    end
  end

  actions do
    default_accept [:name]
    defaults [:read, :create, :update, :destroy]
  end

  policies do
    policy action_type(:read) do
      authorize_if relates_to_actor_via(:owner)
    end
  end

  attributes do
    uuid_v7_primary_key :id
    attribute :name, :string, allow_nil?: false
    timestamps()
  end

  relationships do
    belongs_to :owner, MyApp.Accounts.User do
      allow_nil? false
      public? true
    end
  end

  identities do
    identity :unique_name_per_owner, [:owner_id, :name]
  end
end
```

## Action patterns

- Use `default_accept` when create and update actions share the same allowed fields.
- Use `change relate_actor(:owner)` or similar ownership wiring when the actor should own the record.
- Use action arguments for inputs like `parent_id` that are not persisted as plain accepted attributes.

```elixir
create :create do
  argument :parent_id, :uuid, allow_nil?: false
  change relate_actor(:owner)
  change manage_relationship(:parent_id, :parent, type: :append)
end
```

## Atomicity

- Prefer atomic validations and changes.
- If a custom validation or change breaks atomicity, implement the matching `atomic/3` behavior first.
- Reach for `require_atomic? false` only when the behavior cannot be expressed atomically.
