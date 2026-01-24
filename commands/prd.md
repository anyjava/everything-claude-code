---
allowed-tools: Read, Write, Bash, WebSearch, WebFetch, AskUserQuestion
description: Generate PRD documents from spec file
argument-hint: <spec-file> [additional-requirements]
---

# PRD Generator

Transform specification documents into actionable, structured PRD (Product Requirements Document) sets with issue-ready tasks.

## Input

**Required:** `$1` - Path to specification file (e.g., `./specs/auth-spec.md`)

**Optional:** `$2...` - Additional requirements or instructions from user

## Step 1: Validate Input

```text
Spec file: $1
Additional requirements: $2
```

If no spec file is provided, ask the user:
- Use AskUserQuestion to request the spec file path
- Suggest running `/interview` first if they don't have a spec

Read the spec file and verify it contains sufficient information to generate PRD.

## Step 2: Determine Project Prefix

Generate a unique 2-4 character prefix from the spec topic:

| Spec File | Prefix |
|-----------|--------|
| `user-authentication-spec.md` | `AUTH` |
| `payment-gateway-spec.md` | `PAY` |
| `notification-system-spec.md` | `NOTIF` |
| `ai-chat-assistant-spec.md` | `CHAT` |

## Step 3: Initialize PRD Structure

Use the **prd-scripts** skill to initialize:

```bash
./run.sh scripts/init_prd.py <spec-file-path> --prefix <PREFIX>
```

This creates `<spec-dir>/prd/` with `00-overview.md` and `.prd-config.json`.

## Step 4: Analyze and Generate Features

1. Read the spec document completely
2. **Research if needed**: Use WebSearch/WebFetch to clarify technical requirements
3. Consider additional user requirements if provided
4. Identify major features/components
5. For each feature:

   ```bash
   ./run.sh scripts/add_feature.py <prd-dir> --id F01 --name "feature-name"
   ```

6. Fill in the content for each generated document

## Step 5: Complete the Documents

For each document:

1. Fill in all `[TODO:]` sections
2. Ensure all task IDs follow `<PREFIX>-F##-T##` format
3. Update the Feature Map and Task Index in `00-overview.md`
4. Apply any additional requirements specified by user

## Step 6: Validate

```bash
./run.sh scripts/validate_prd.py <prd-dir>
```

## ID System

### Format

```text
<PREFIX>-F<##>-T<##>
```

### Examples

- `AUTH-F01-T01`: Authentication feature 1, task 1
- `PAY-F02-T03`: Payment feature 2, task 3

### Rules

1. **Prefix**: 2-4 characters derived from spec topic
2. **Feature ID**: F01, F02... (zero-padded)
3. **Task ID**: T01, T02... (per feature)
4. **Stability**: IDs never change once assigned

## Difficulty Scale

| Level | Description | Typical Scope |
|-------|-------------|---------------|
| 1 | Trivial | Config change, simple UI tweak |
| 2 | Easy | Single file change, straightforward logic |
| 3 | Medium | Multiple files, some complexity |
| 4 | Hard | Cross-cutting concerns, complex logic |
| 5 | Very Complex | Architectural changes, high risk |

## Guidelines

1. **Split wisely**: Each feature should be independently implementable
2. **Order by dependencies**: Features that others depend on come first
3. **Be specific**: Vague tasks are hard to implement
4. **Include acceptance criteria**: Define "done" clearly for each task
5. **Keep tasks granular**: Each task = one issue = one PR ideally
6. **Use consistent IDs**: Every task must have `<PREFIX>-F##-T##`

---

Each task in the PRD is designed to be directly convertible to an issue/ticket in your project tracker.

---

Begin by reading the spec file: $1
