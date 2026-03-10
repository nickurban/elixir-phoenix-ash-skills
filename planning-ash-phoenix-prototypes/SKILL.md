---
name: planning-ash-phoenix-prototypes
description: Plan pragmatic Ash-Phoenix-LiveView implementations with concise, non-redundant specifications. Use when creating implementation plans for Ash resources, domains, state machines, or Phoenix LiveView features.
---

# Planning Ash-Phoenix-LiveView Prototypes

## Pre-Planning: Discover Extensions and Libraries

**Before writing the plan**, search for relevant Ash extensions and well-maintained Elixir libraries that could simplify the implementation.

### Discovery Process

1. **Identify key requirements** from the user's request (state management, audit trails, file uploads, etc.)
2. **Search for Ash extensions** that address these needs:
   - Check `mix hex.search ash_` for available extensions
   - Review project dependencies for already-installed extensions
   - Consult Ash documentation for official extensions
3. **Search for Elixir libraries** if no Ash extension exists:
   - Use `mix hex.search` for relevant libraries
   - Prefer well-maintained libraries (recent updates, good documentation)
4. **Ask the user** before finalizing the plan only when the choice:
   - Adds a new dependency or extension
   - Changes the architecture materially
   - Has meaningful trade-offs the user should choose between
5. Otherwise, prefer the simplest approach that fits the repo rules and mention the choice briefly in the plan

### Common Ash Extensions to Consider

- **State Management**: `ash_state_machine` - For resources with state transitions
- **Audit Trails**: 
  - `ash_events` - For tracking changes and events
  - `ash_paper_trail` - Alternative audit trail solution
- **File Uploads**: `ash_file` - For handling file attachments
- **Money/Currency**: `ash_money` - For financial amounts (already common)
- **Slug Generation**: `ash_slug` - For URL-friendly identifiers
- **Full-Text Search**: `ash_postgres_full_text_search` - For search functionality
- **JSON API**: `ash_json_api` - For JSON API compliance
- **GraphQL**: `ash_graphql` - For GraphQL APIs

### Example Discovery Questions

**State Management:**
> "For tracking state on Order (or ApprovalRequest, etc.), should we use `ash_state_machine` or manage state manually with status attributes?"

**Audit Trails:**
> "For audit trail, should we use `ash_events`, `ash_paper_trail`, or create a custom event resource?"

**File Handling:**
> "For file uploads, should we use `ash_file` or handle files manually?"

### When to Ask

- **Ask** when a new dependency, extension, or architectural direction is being introduced
- **Don't ask** for routine choices when the repo already has a preferred pattern or standard tool
- **Prefer the simplest fit** when one option is clearly aligned with repo rules and existing conventions
- **Ask early** before writing detailed plan sections that depend on the choice

## Core Principles

1. **Discover tools first** - Search for extensions/libraries before planning implementation details
2. **Don't repeat what's in skills** - If information is in the project's Ash/working-with-ash skill, reference it rather than repeating it
3. **Let Ash handle defaults** - Don't specify defaults that Ash/AshStateMachine already provides
4. **Use the right tool for the job** - Attributes for nullability, policies for authorization, state machine for transitions
5. **Be concise** - Avoid redundant steps (e.g., don't list individual mix tasks covered by `mix precommit`)

## Common Anti-Patterns to Avoid

### Policies vs Validations

- **Policies handle:** Authorization, actor checks, flags on the actor (e.g., checking if actor has approver role)
- **Validations handle:** Business logic (e.g., amount must be positive)
- **Attributes handle:** Nullability via `allow_nil?`
- **References handle:** foreign key constraints
- **Identities handle:** unique
- AshStateMachine sets its own initial state and does its own transition validation

## Planning Checklist

When creating an Ash implementation plan:

1. **Before planning**: Search for relevant Ash extensions and Elixir libraries
2. Ask the user about extensions/libraries only when the choice adds a dependency or has meaningful trade-offs
3. Reference the Ash skill for standard patterns (don't repeat them)
4. Move authorization checks to policies section, not action validations
5. Use appropriate extensions (e.g., AshStateMachine for state management)
6. Specify calculation behavior clearly (what it returns, not just when it runs)
7. Consolidate final steps (run `mix precommit`, not tasks it already contains)
8. Remove redundant notes about defaults already documented in skills
9. **Review the completed plan**:
   - Is this the right approach? (Consider alternatives, extensions, simpler solutions)
   - Is the plan well-drafted? (Clear, organized, easy to follow)
   - Is it concise? (No redundant information, no repetition of skill content)
   - Are there any anti-patterns from the "Common Anti-Patterns" section?
   - Does it follow all principles in this skill?

## Example: Clean Action Definition

```markdown
- [ ] Add `update :confirm` action (state machine transition)
  - [ ] Accept: `[:notes]`
  - [ ] Send confirmation email (custom change)
  - [ ] Note: State transition handled by state machine. Actor authorization handled by policies.
```

This is concise and focuses on what's unique to this action (sending the email), not what's handled elsewhere.
