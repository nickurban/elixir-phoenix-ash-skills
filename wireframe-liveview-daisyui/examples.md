## Examples

### Simple “index” page with a table and toolbar

```heex
<Layouts.app flash={@flash} current_scope={@current_scope}>
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

- Prefer:
  - `bg-base-100`, `bg-base-200`, `border-base-300`
  - `text-base-content`, `text-base-content/70`
  - DaisyUI semantic classes: `card`, `btn`, `alert`, `badge`, `tabs`
- Avoid hard-coded grays like `text-gray-600` unless you verify both themes.

