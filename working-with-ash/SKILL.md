---
name: working-with-ash
description: Create and update Ash resources, domains, relationships, references, postgres configuration, actions, validations, and policies. Use when working with Ash Framework resources, domains, migrations, or when creating/updating database schemas in this Phoenix application.
---

# Working with Ash Framework

## Migrations

**CRITICAL**: This project uses Ash for data layer management, not Ecto directly.

- **Always** use `mix ash.codegen <change_name>` followed by `mix ash.migrate` to change the db schema
- **Never** write migration files directly
- **Never** use `mix ecto.migrate`
- **Never** edit generated migrations **always** update the resource and re-run ash.codegen instead

## Critical Ash Guardrails

- When a feature is built on Ash resources/domains, do not fall back to generic Ecto-first guidance unless the file actually uses Ecto directly.
- LiveViews should use domain-level interfaces, `form_to_*` helpers, and `AshPhoenix.Form` flows rather than Phoenix-default changeset/controller patterns.
- Authorization belongs in Ash policies, AshAuth flows, and `Ash.can?`; do not duplicate the same permission logic in LiveViews or controllers.
- For multi-step work, prefer plan and review guidance that keeps Ash resources, forms, and policies as the source of truth.

## Resource Structure

Follow this section order (configured in `config.exs`):

1. `postgres` - database configuration
2. `actions` - CRUD operations
3. `policies` - authorization rules
4. `attributes` - fields
5. `relationships` - associations
6. `calculations` - derived values
7. `identities` - unique constraints

## Basic Resource Setup

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
  end

  actions do
    default_accept [:name]
    defaults [:read, :create, :update, :destroy]
  end

  attributes do
    uuid_v7_primary_key :id
    timestamps()
  end
end
```

## Attributes

- **Use UUIDv7** as default ID type
- **Use `timestamps/1`** instead of defining `inserted_at` and `updated_at`
- **Always set `allow_nil?: false`** for attributes unless they need to be nullable
- For JSONB data: `attribute :data, :map, default: %{}, allow_nil?: false`

## Relationships

### Defining Relationships

```elixir
relationships do
  belongs_to :owner, MyApp.Accounts.User do
    allow_nil? false
    public? true
  end

  has_many :posts, MyApp.Posts.Post do
    public? true
  end
end
```

### Foreign Key References

Add `references` inside `postgres` block to configure foreign key behavior:

```elixir
postgres do
  references do
    reference :independent_resource, on_delete: :restrict
    reference :parent_resource, on_delete: :delete
    reference :optional_resource, on_delete: :nilify
  end
end
```

Common `on_delete` options:
- `:restrict` - prevent deletion if referenced
- `:delete` - cascade delete
- `:nilify` - set foreign key to nil

## Identities (Unique Constraints)

**Prefer `identities` to `custom_indexes`**:

```elixir
identities do
  identity :unique_version, [:resource_id, :version]
  identity :unique_email, [:email]
end
```

## Actions

### Atomic actions and `require_atomic?`

- Prefer atomic actions.
- If a custom validation/change breaks atomicity, implement `atomic/3` first (matching `validate/3` or `change/3` behavior).
- Use `require_atomic? false` only when truly necessary, and add a brief reason.

### Create Actions

Create actions commonly use relate_actor to set a user reference.

```elixir
create :create do
  accept [:name, :description]
  change relate_actor(:owner)
end
```

### Using `default_accept`

If create and update actions have same accepts, use `default_accept`:

```elixir
actions do
  default_accept [:name, :description]

  create :create do
    # accepts [:name, :description] automatically
  end

  update :update do
    # accepts [:name, :description] automatically
  end
end
```

### Action Arguments

Use arguments for values that aren't attributes:

```elixir
create :create do
  argument :parent_id, :uuid, allow_nil?: false
  change manage_relationship(:parent_id, :parent, type: :append)
end
```

## AshPhoenix Forms in LiveView

Prefer generated form helpers over Phoenix-default form setup:

```elixir
form =
  MyDomain.form_to_create_thing(actor: current_user, as: "thing")
  |> to_form()
```

For record updates:

```elixir
form =
  MyDomain.form_to_update_thing(record, actor: current_user, as: "thing")
  |> to_form()
```

In event handlers, validate and submit the stored Ash form:

```elixir
def handle_event("validate", %{"thing" => params}, socket) do
  form = AshPhoenix.Form.validate(socket.assigns.form, params)

  {:noreply, assign(socket, :form, form)}
end

def handle_event("save", %{"thing" => params}, socket) do
  case AshPhoenix.Form.submit(socket.assigns.form, params: params) do
    {:ok, thing} -> {:noreply, assign(socket, :thing, thing)}
    {:error, form} -> {:noreply, assign(socket, :form, form)}
  end
end
```

Guidelines:

- Prefer `form_to_*` interfaces generated from Ash code interfaces
- Avoid `AshPhoenix.Form.for_create/for_update` when a `form_to_*` helper exists
- Do not introduce Ecto changesets or `cast/4` just to power a LiveView form
- Keep the `to_form/1` result in assigns and pass that form back through `AshPhoenix.Form.validate/2` and `AshPhoenix.Form.submit/2`

### Custom Validations in Actions

```elixir
create :create do
  validate fn changeset, _context ->
    value = Ash.Changeset.get_attribute(changeset, :field)
    if valid?(value) do
      :ok
    else
      {:error, field: :field, message: "must be valid"}
    end
  end
end
```

### Using Validation Modules

```elixir
update :lock do
  validate MyApp.Validations.EnsureUnlocked
end
```

## Policies & Authorization

- Prefer built-in checks (`relates_to_actor_via`, `actor_attribute_equals`, etc.) over custom checks
- **Never** perform permission checks in validations, **always** perform them via policies.
- Prefer AshAuthentication/AshAuthenticationPhoenix flows for auth-related behavior instead of hand-rolled controller/session logic unless the existing code already requires a custom path.

### Setting Ownership on Create

```elixir
actions do
  create :create do
    change relate_actor(:created_by)
  end
end
```

### Standard Ownership Policies

For resources where all users can create, but users only read their own:

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

### Nested Relationship Policies

```elixir
policy action_type(:read) do
  authorize_if relates_to_actor_via([:parent, :owner])
end
```

### System Operations

When the system performs actions not on behalf of a user, pass `authorize?: false`:

```elixir
Ash.create(changeset, authorize?: false)
MyDomain.create_thing(params, authorize?: false)
```

**Important**: Do not use `authorize_if always()` in policies for system actions. Instead, put `authorize?: false` in the caller. This keeps authorization logic explicit at the call site rather than hidden in the resource definition.

## Code Interfaces

### Domain-Level Interfaces (Cross-Domain)

Define in domain's `resources` block:

```elixir
defmodule MyApp.Domain do
  use Ash.Domain

  resources do
    resource MyResource do
      define :create_thing, args: [:name], action: :create
      define :get_thing_by_id, action: :read, get_by: [:id]
      define :list_things, action: :read
    end
  end
end
```

Usage:
```elixir
MyApp.Domain.create_thing(name, actor: user)
MyApp.Domain.get_thing_by_id!(id, load: [:relationship], actor: user)
```

### Resource-Level Interfaces (Within Domain)

```elixir
code_interface do
  define :open, args: [:subject]
  define :close, action: :close
end
```

Usage: `MyResource.open(subject)`

### The `get_by` Option

Automatically creates functions using primary read action with applied filter:

```elixir
define :get_thing_by_id, action: :read, get_by: [:id]
```

Supports dynamic loading and filtering:
```elixir
MyDomain.get_thing_by_id!(id, 
  load: [relationship: [:nested]],
  query: [filter: [status: :active]]
)
```

### LiveViews Must Use Domain Interfaces

**Never** call resource-level code interfaces from LiveViews. Always use domain-level interfaces:

```elixir
# ❌ Don't do this
MyResource.list_by_user(user)

# ✅ Do this
MyApp.Domain.list_things_by_user(user)
```

**Never** use `Ash.Query`, `Ash.read`, `Ash.create`, `Ash.update` directly in LiveViews.

## Calculations

Derived values using expressions:

```elixir
calculations do
  calculate :status, :atom, expr(if(is_nil(locked_at), :draft, :new)) do
    constraints one_of: [:draft, :new]
    public? true
  end
end
```

## Custom Changes

Implement `Ash.Resource.Change` behavior:

```elixir
defmodule MyApp.Changes.SetTimestamp do
  use Ash.Resource.Change

  def change(changeset, _opts, _context) do
    Ash.Changeset.change_attribute(changeset, :timestamp, DateTime.utc_now())
  end
end
```

For operations needing database queries in transaction:

```elixir
def change(changeset, _opts, _context) do
  Ash.Changeset.before_action(changeset, fn changeset ->
    # Query data, modify changeset
    max_value = get_max_value()
    Ash.Changeset.force_change_attribute(changeset, :value, max_value + 1)
  end)
end
```

## Checking Resource Existence

**Always** use `Ash.exists?()` rather than fetching:

```elixir
MyResource
|> Ash.Query.filter(expr(id == ^id and created_by_id == ^actor.id))
|> Ash.exists?(authorize?: false)
```

**Never** use `Ash.read_one/2` or `Ash.get!/2` when you only need to verify existence.

## Pattern Matching on Ash Results

**Always** match on what the function actually returns (check using e.g. `mix help Ash.read_one`):

```elixir
# ✅ Correct
case Ash.read_one(query) do
  {:ok, nil} -> :not_found
  {:ok, record} -> record
  {:error, error} -> {:error, error}
end

# ❌ Wrong - read_one never returns nil
case Ash.read_one(query) do
  nil -> :not_found
  {:ok, record} -> record
  record -> record
  _ -> :error
end
```

## Testing

- **Never** use `Ash.create` for resources that have a generator. **Always** use a generator
- Pass `authorize?: false` in tests when checking if records were created
- **Never** use fixtures (except vcr)

## Efficiency

- Use bulk actions when inserting or updating many records
- Prefer to sort, filter, and paginate at the DB level, not in Elixir
- Use `Ash.exists?()` for existence checks, not full reads

## Ash.money

`Money.new/1` takes an amount in dollars, not cents, as an integer or decimal. Floats are not allowed.

## Domain Configuration

Register domains in `config/config.exs`:

```elixir
config :my_app,
  ash_domains: [MyApp.Accounts, MyApp.Domain]
```

## Common Patterns

### Version Incrementing

Use database-level atomic operations (PostgreSQL functions) for version incrementing to avoid race conditions.

### Status from Timestamps

Calculate status from timestamp presence rather than redundant fields:

```elixir
calculate :status, :atom, expr(if(is_nil(locked_at), :draft, :locked))
```

## Additional Resources

- Consult Ash docs: 
  - deps/ash/usage-rules/*
  - `mix help <foo>` (foo is Module, Module.func etc)
- Search other docs
  - To search, consult `mix help usage_rules.search_docs
- Check AshPostgres usage rules: `deps/ash_postgres/usage-rules.md`

