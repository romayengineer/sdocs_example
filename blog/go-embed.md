# Embedding Files in Go


📌 **Status: published`

| | |
|---|---|
| **Date** | 2026-07-30 |
| **Last updated** | 2026-07-30 |
| **Category** | tutorial |
| **Difficulty** | intermediate |
| **Reading time** | 5 min |
| **Word count** | ~700 |
| **Author** | Structured Docs |
| **Language** | Go |
| **Version** | Go 1.16+ |
| **License** | CC-BY-4.0 |

## Prerequisites

- Basic Go syntax
- File I/O in Go

## Overview

The `embed` package (introduced in Go 1.16) allows embedding files and directories into the Go binary at compile time.

## Basic Usage

Use the `//go:embed` directive to embed files:

```go
import (
    _ "embed"
    "fmt"
)

//go:embed hello.txt
var hello string

func main() {
    fmt.Println(hello)
}
```

### Embedding into []byte

```go
//go:embed logo.png
var logo []byte
```

### Embedding Multiple Files

```go
import "embed"

//go:embed templates/*
var templateFS embed.FS

func main() {
    data, _ := templateFS.ReadFile("templates/index.html")
    fmt.Println(string(data))
}
```

### Embedding a Directory Tree

```go
//go:embed static
var staticFS embed.FS

// Access files
entries, _ := staticFS.ReadDir("static")
for _, e := range entries {
    fmt.Println(e.Name())
}
```

### Path Patterns

```go
//go:embed all:data       // recursively, including files starting with .
//go:embed *.txt          // all .txt files in current directory
//go:embed file1 file2    // specific files
```

Patterns cannot use `..` or absolute paths. They are relative to the source file's directory.

### Use Cases

- **Web servers**: embed HTML templates, CSS, JS, images
- **CLI tools**: embed default configs, help text, migration files
- **Static analysis**: embed rulesets, pattern files
- **Testing**: embed test fixtures

## Examples

```go
//go:embed templates
var tmplFS embed.FS

func main() {
    tmpl := template.Must(template.ParseFS(tmplFS, "templates/*.html"))

    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        tmpl.ExecuteTemplate(w, "index.html", nil)
    })

    // Serve embedded static files
    http.Handle("/static/", http.FileServer(http.FS(tmplFS)))
    log.Fatal(http.ListenAndServe(":8080", nil))
}
```

## Summary

Embedding produces a single self-contained binary — no file dependencies at runtime, simpler deployment, and consistent behavior across environments.

## Related Posts

- File I/O in Go
- Building HTTP Servers and Clients in Go

**Tags:** `go` `embed` `files` `binary` 

## References

 - [Go blog: Embed](https://go.dev/blog/embed)
 - [Go embed docs](https://pkg.go.dev/embed)

