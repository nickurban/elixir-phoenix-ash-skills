## Examples

### Simple “index” page with a table and toolbar

Adapt the root layout and assigns to the host app. If the repo uses a wrapper like `<Layouts.app ...>`, preserve that shape; otherwise keep the example inside the app's existing layout conventions.

```heex
<Layouts.app flash={@flash}>
  <div id="page-users" class="container mx-auto max-w-5xl px-4 py-6">
    <div class="flex items-center justify-between gap-4">
      <div>
        <h1 class="text-xl font-semibold">Users</h1>
        <p class="text-sm text-base-content/70">Manage access</p>
      </div>
      <div class="flex items-center gap-2">
        <.link navigate={~p"/users/new"} class="btn btn-primary">New user</.link>
      </div>
    </div>

    <div class="mt-6 card bg-base-100 border border-base-300">
      <div class="card-body">
        <.table id="users-table" rows={@users}>
          <:col :let={user} label="Email">{user.email}</:col>
          <:col :let={user} label="Role">{user.role}</:col>
          <:action :let={user}>
            <.link navigate={~p"/users/#{user.id}"} class="link">View</.link>
          </:action>
        </.table>
      </div>
    </div>
  </div>
</Layouts.app>
```

### Theme-safe color usage

- Prefer `bg-base-100`, `bg-base-200`, `border-base-300`
- Prefer `text-base-content`, `text-base-content/70`
- Prefer DaisyUI semantic classes such as `card`, `btn`, `alert`, `badge`, and `tabs`
- Avoid hard-coded grays like `text-gray-600` unless you verify both themes
