# AGENTS.md — Structured Docs Guide

## Overview

This project uses [`sd`](https://github.com/romayengineer/structured-docs) to generate Markdown documentation from structured YAML data and Go templates.

**Flow:**
```
YAML data files  →  Go templates  →  compiled .md output
(schemas validate data shape)
```

## Directory Layout

```
sdocs_example/
├── sdocs/
│   ├── structured.yml          # Config: paths + template_order
│   ├── schema/                 # YAML schemas (field definitions)
│   │   ├── post.yml
│   │   └── tech-post.yml
│   ├── templates/              # Go templates (*.template.md)
│   │   ├── post.template.md
│   │   └── tech-post.template.md
│   └── data/blog/              # YAML data files (the actual content)
│       ├── go-context.yml
│       ├── go-goroutines.yml
│       └── ...
└── docs/blog/                  # Generated output (DO NOT edit manually)
    └── *.md
```

## Workflow

1. **Define a schema** — create `schema/<type>.yml` with fields, types, and `required` flags
2. **Create a template** — create `templates/<name>.template.md` using Go template syntax
3. **Write data** — create `data/<category>/<slug>.yml` with `type: <schema-name>` and field values
4. **Generate** — `cd sdocs && sd -config structured.yml` (config paths are relative to cwd)
5. **Preview** — `.md` files open in VS Code markdown preview automatically

## Schemas

Available field types: `string`, `int`, `float`, `bool`, `[]string`, `[]int`, `map[string]string`

```yaml
# schema/my-type.yml
description: "Description of this document type"
fields:
  - name: title
    type: string
    required: true
  - name: tags
    type: "[]string"
    required: false
```

**IMPORTANT:** Fields default to `required: true`. Always set `required: false` explicitly for optional fields.

## Templates

- File naming: `*.template.md` (produces `.md` output)
- Uses Go's `text/template` syntax
- Data fields are accessed as `{{ .fieldName }}`

### CRITICAL: No template functions

The `extractFields` parser uses `parse.Parse` which does **not** register Go template functions. **No function calls are allowed** — not `eq`, `ne`, `and`, `not`, `printf`, `len`, etc.

```gotemplate
# BROKEN — all of these will fail to parse:
{{ if eq .status "published" }}
{{ if not .featured }}
{{ if and .a .b }}
{{ printf "%s" .title }}
```

Only use:
- Field access: `{{ .field }}`, `{{ .nested.field }}`
- Actions: `if`, `range`, `with`
- Variables: `{{ $var := .field }}`, `{{ range $k, $v := .map }}`

**Workarounds for common cases:**

| Goal | What to do instead |
|---|---|
| Check if field equals a value | Can't — restructure your data; use a separate boolean field or keep the value in a separate `is_*` field |
| Check if field is NOT a value | Can't — show the field unconditionally when present, or use a different field convention |
| Conditional display | Use `{{ if .field }}` to check presence, and design your data conventions around it |

### Template Matching

Templates are matched to data files via `template_order` in `structured.yml`:

```yaml
template_order:
  - tech-post.template.md   # tried first
  - post.template.md        # fallback
```

The resolver iterates `template_order` and picks the **first** template whose auto-extracted `RequiredFields` are a subset of the data's type schema fields.

**Strategy:** Put more specific templates first. A broader template (fewer required fields) will match any type that has those fields, so place it last.

## Data Files

```yaml
# data/blog/my-post.yml
type: my-type           # must match a schema filename (minus .yml)
title: My Post
body: |
  Markdown content here...

  ## Section

  ```go
  code blocks work
  ```

tags:
  - tag1
  - tag2
```

- The `type` field is **required** and must match a schema file name
- All `required: true` fields must be present
- Optional fields can be omitted entirely

## Running sd

```sh
# From sdocs/ directory:
sd -config structured.yml         # generate (incremental)
sd -clean -config structured.yml  # clean + regenerate
sd -watch -config structured.yml  # watch for changes
```

If `sd` is not in PATH:
```sh
GOPROXY=direct go install github.com/romayengineer/structured-docs/cmd/sd@main
~/go/bin/sd -config structured.yml
```

## VS Code Integration

Create `.vscode/settings.json` to auto-open `.md` files in preview mode:

```json
{
  "workbench.editorAssociations": {
    "*.md": "vscode.markdown.preview.editor"
  }
}
```

Create `.vscode/tasks.json` for the watch task:

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "sd watch",
      "type": "shell",
      "command": "sd -config structured.yml -watch",
      "options": {
        "cwd": "${workspaceFolder}/sdocs"
      },
      "group": "build",
      "isBackground": true,
      "problemMatcher": []
    }
  ]
}
```

### ⚠️ DO NOT use `sd -clean` with `output_dir: ..`

The `-clean` flag calls `RemoveAll` on the output directory. With `output_dir: ..` (the parent), this **deletes the entire project** — all schemas, templates, data files, and git history. Always regenerate manually:

```sh
rm -rf ../blog && sd -config structured.yml    # if output_dir is ..
sd -clean -config structured.yml               # safe only when output_dir is a subdirectory
```

## Common Pitfalls

| Pitfall | Fix |
|---|---|
| `sd` says `function "eq" not defined` | Don't use any template functions — only `if`, `range`, `with`, field access, variables |
| `missing required field` for optional field | Add `required: false` to the schema field definition |
| Wrong template is used for a data file | Reorder `template_order` — more specific templates first |
| `sd` command not found | Use full path `~/go/bin/sd` or install with `go install` |
| Generated `.md` has extra blank lines | This is normal Go template whitespace behavior — use `{{- ` or ` -}}` trimming if needed |
| Data file not in output | Check that `type` matches a schema filename exactly (case-sensitive) |
