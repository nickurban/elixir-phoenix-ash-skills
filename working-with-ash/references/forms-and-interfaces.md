# Forms and Interfaces

## Domain interfaces

Expose action entry points from the domain for cross-resource and UI-facing usage.

```elixir
defmodule MyApp.Domain do
  use Ash.Domain

  resources do
    resource MyApp.Domain.Thing do
      define :create_thing, action: :create
      define :update_thing, action: :update
      define :get_thing_by_id, action: :read, get_by: [:id]
    end
  end
end
```

## Resource interfaces

Use `code_interface` for helper actions that are primarily consumed inside the same domain.

```elixir
code_interface do
  define :open, args: [:subject]
  define :close, action: :close
end
```

## LiveView form flow

Prefer generated `form_to_*` functions:

```elixir
form =
  MyApp.Domain.form_to_create_thing(actor: current_user, as: "thing")
  |> to_form()
```

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

## Form rules

- Prefer `form_to_*` helpers over `AshPhoenix.Form.for_create/for_update` when the helpers exist.
- Keep the form in assigns and pass that same form through validate/submit.
- Do not fall back to Phoenix-default changesets just to make a form work.
