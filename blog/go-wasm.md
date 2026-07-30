# WebAssembly with Go


📌 **Status: published`

| | |
|---|---|
| **Date** | 2026-07-30 |
| **Last updated** | 2026-07-30 |
| **Category** | tutorial |
| **Difficulty** | advanced |
| **Reading time** | 7 min |
| **Word count** | ~950 |
| **Author** | Structured Docs |
| **Language** | Go |
| **Version** | Go 1.21 |
| **License** | CC-BY-4.0 |

## Prerequisites

- Basic Go syntax
- Building HTTP Servers and Clients in Go

## Overview

Go can compile to WebAssembly (Wasm), allowing Go programs to run in the browser at near-native speed.

## Basic Usage

### Hello World in Wasm

```go
package main

import "syscall/js"

func main() {
    doc := js.Global().Get("document")
    body := doc.Get("body")
    p := doc.Call("createElement", "p")
    p.Set("innerText", "Hello from Go!")
    body.Call("appendChild", p)
}
```

### Compiling

```sh
GOOS=js GOARCH=wasm go build -o main.wasm main.go
```

Copy the JavaScript glue from the Go installation:

```sh
cp "$(go env GOROOT)/misc/wasm/wasm_exec.js" .
```

### Serving the Page

```html
<script src="wasm_exec.js"></script>
<script>
    const go = new Go();
    WebAssembly.instantiateStreaming(
        fetch("main.wasm"), go.importObject
    ).then(result => {
        go.run(result.instance);
    });
</script>
```

### Interacting with the DOM

```go
func setButton() {
    doc := js.Global().Get("document")
    btn := doc.Call("createElement", "button")
    btn.Set("innerText", "Click me")
    btn.Call("addEventListener", "click", js.FuncOf(func(this js.Value, args []js.Value) any {
        js.Global().Get("console").Call("log", "Button clicked!")
        return nil
    }))
    doc.Get("body").Call("appendChild", btn)
}
```

### Calling JavaScript from Go

```go
// Call JS functions
result := js.Global().Call("JSON.parse", `{"key": "value"}`)
fmt.Println(result.Get("key").String())

// Call JS methods
js.Global().Get("Math").Call("random")
```

## Best Practices

- Porting existing Go libraries to the browser
- CPU-intensive computation (image processing, parsing, game logic)
- Sharing code between backend (Go) and frontend (Wasm)
- When you prefer Go's type safety and tooling over JavaScript

## Common Pitfalls

- No `os` package (no file system, network in browser)
- No `net/http` server (client works with browser's fetch API)
- Single-threaded (no goroutine parallelism, though concurrency works)
- `syscall/js` is verbose and not idiomatic Go

## Advanced Topics

### Exposing Go Functions to JavaScript

```go
func main() {
    done := make(chan struct{})

    js.Global().Set("add", js.FuncOf(func(this js.Value, args []js.Value) any {
        a := args[0].Int()
        b := args[1].Int()
        return a + b
    }))

    <-done // keep the program running
}
```

Then call from JS: `add(3, 4)` returns `7`.

### Performance Considerations

- Wasm files are large (~2MB minimum) — use `-ldflags="-s -w"` to strip debug info
- Go's full runtime (GC, scheduler) is included — not ideal for tiny scripts
- `tinygo` produces much smaller Wasm binaries for simple programs
- No direct DOM access — all DOM manipulation goes through `syscall/js` which is slow

## Summary

Wasm support in Go is mature but niche. For most web development, JavaScript/TypeScript remains the practical choice. Go+Wasm excels for specific use cases where code reuse or performance matters.

## Related Posts

- Building HTTP Servers and Clients in Go
- Modules and Packages in Go

**Tags:** `go` `wasm` `webassembly` `browser` 

## References

 - [Go Wasm wiki](https://github.com/golang/go/wiki/WebAssembly)
 - [TinyGo Wasm](https://tinygo.org/docs/guides/webassembly/)

