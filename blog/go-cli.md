# Building CLI Applications in Go


📌 **Status: published`

| | |
|---|---|
| **Date** | 2026-07-30 |
| **Last updated** | 2026-07-30 |
| **Category** | tutorial |
| **Difficulty** | intermediate |
| **Reading time** | 6 min |
| **Word count** | ~850 |
| **Author** | Structured Docs |
| **Language** | Go |
| **Version** | Go 1.21 |
| **License** | CC-BY-4.0 |

## Prerequisites

- Modules and Packages in Go
- Error Handling in Go

## Overview

Go is an excellent language for building command-line tools. The standard library's `flag` package handles basic CLI needs, while third-party libraries offer more advanced features.

## Basic Usage

### Using the flag Package

```go
import "flag"

func main() {
    name := flag.String("name", "world", "a name to greet")
    count := flag.Int("count", 1, "number of times to greet")
    verbose := flag.Bool("verbose", false, "enable verbose output")
    flag.Parse()

    for i := 0; i < *count; i++ {
        if *verbose {
            fmt.Printf("[%d/%d] ", i+1, *count)
        }
        fmt.Printf("Hello, %s!\n", *name)
    }
}
```

Run with:

```sh
go run main.go -name Alice -count 3 -verbose
```

### Subcommands

Use `flag.FlagSet` for subcommands like `git commit -m "msg"`:

```go
func main() {
    if len(os.Args) < 2 {
        fmt.Println("usage: app <command>")
        return
    }

    switch os.Args[1] {
    case "greet":
        greetCmd := flag.NewFlagSet("greet", flag.ExitOnError)
        name := greetCmd.String("name", "world", "")
        greetCmd.Parse(os.Args[2:])
        fmt.Printf("Hello, %s!\n", *name)

    case "version":
        fmt.Println("v1.0.0")
    }
}
```

### Environment Variables

```go
func getConfig() {
    port := os.Getenv("PORT")
    if port == "" {
        port = "8080"
    }

    dbURL := os.Getenv("DATABASE_URL")
    if dbURL == "" {
        log.Fatal("DATABASE_URL is required")
    }
}
```

## Best Practices

- **Use `os.Exit` appropriately** — exit code 0 for success, non-zero for errors
- **Write to stderr for errors** — `os.Stderr` or `log` package
- **Support `-h`/`--help`** — the `flag` package does this automatically
- **Support `--version`** — use `ldflags` to inject version at build time: `go build -ldflags="-X main.version=$(git describe)"`
- **Color output sparingly** — use `autocorrection` or check for terminal capability

## Advanced Topics

### Organizing Commands with Third-Party Libraries

Cobra, the most popular CLI framework used by Kubernetes, Hugo, and GitHub CLI:

```go
var rootCmd = &cobra.Command{
    Use:   "myapp",
    Short: "MyApp is a CLI tool",
    Run: func(cmd *cobra.Command, args []string) {
        fmt.Println("Hello from myapp")
    },
}

var greetCmd = &cobra.Command{
    Use:   "greet [name]",
    Short: "Greet someone",
    Args:  cobra.MinimumNArgs(1),
    Run: func(cmd *cobra.Command, args []string) {
        fmt.Printf("Hello, %s!\n", args[0])
    },
}

func main() {
    rootCmd.AddCommand(greetCmd)
    rootCmd.Execute()
}
```

### Progress Bars

For long-running operations:

```go
bar := progressbar.New(100)
for i := 0; i < 100; i++ {
    bar.Add(1)
    time.Sleep(50 * time.Millisecond)
}
```

## Summary

Go's compiled binaries, fast startup, and cross-platform support make it the language of choice for modern CLI tools.

## Related Posts

- Modules and Packages in Go
- Building HTTP Servers and Clients in Go

**Tags:** `go` `cli` `command-line` `tools` 

## References

 - [Cobra](https://github.com/spf13/cobra)
 - [Go flag package docs](https://pkg.go.dev/flag)

