# The sync Package

📌 **Status: published**

| | |
|---|---|
| **Date** | 2026-07-30 |
| **Last updated** | 2026-07-30 |
| **Category** | tutorial |
| **Difficulty** | intermediate |
| **Reading time** | 8 min |
| **Word count** | ~1300 |
| **Author** | Structured Docs |
| **Language** | Go |
| **Version** | Go 1.22 |
| **License** | CC-BY-4.0 |
| **Series** | Go Concurrency (Part 2) |


## Prerequisites

- Goroutines and Channels
- Basic concurrency understanding


## Overview

While channels are Go's high-level concurrency primitive, the `sync` package provides lower-level synchronization tools.

## Basic Usage

### Mutex

Protects shared state from concurrent access:

```go
var mu sync.Mutex
var counter int

func increment() {
    mu.Lock()
    counter++
    mu.Unlock()
}
```

Always use `defer` for `Unlock` in non-trivial functions:

```go
func safeWrite(data map[string]int, key string, val int) {
    mu.Lock()
    defer mu.Unlock()
    data[key] = val
}
```

### RWMutex

Allows multiple concurrent readers but exclusive writers:

```go
var mu sync.RWMutex

func read(key string) int {
    mu.RLock()
    defer mu.RUnlock()
    return cache[key]
}

func write(key string, val int) {
    mu.Lock()
    defer mu.Unlock()
    cache[key] = val
}
```

Use `RLock`/`RUnlock` for read-only operations to avoid contention.

### WaitGroup

Waits for a collection of goroutines to finish:

```go
var wg sync.WaitGroup

for i := 0; i < 10; i++ {
    wg.Add(1)
    go func(id int) {
        defer wg.Done()
        doWork(id)
    }(i)
}

wg.Wait() // blocks until all 10 finish
```

`wg.Add` should be called outside the goroutine to avoid race conditions with `Wait`.

### Once

Ensures a function is executed only once, regardless of how many goroutines call it:

```go
var once sync.Once
var config *Config

func getConfig() *Config {
    once.Do(func() {
        config = loadConfig() // runs exactly once
    })
    return config
}
```

Perfect for lazy initialization and singleton patterns.


## Advanced Topics

### Pool

A scalable pool of reusable objects, useful for reducing allocations:

```go
var bufPool = sync.Pool{
    New: func() any {
        return new(bytes.Buffer)
    },
}

func handle() {
    buf := bufPool.Get().(*bytes.Buffer)
    defer bufPool.Put(buf)
    buf.Reset()
    // use buf...
}
```

Objects in the pool may be garbage collected. Pool is best for temporary, expensive-to-allocate objects.

### Map (sync.Map)

A concurrent-safe map optimized for:

- Write-once, read-many workloads
- Contended keys in different goroutines

```go
var m sync.Map

m.Store("key", "value")
val, ok := m.Load("key")
m.LoadOrStore("key", "default")
m.Delete("key")
m.Range(func(k, v any) bool {
    fmt.Println(k, v)
    return true // continue iteration
})
```

For most use cases, a regular `map` with `sync.Mutex` is simpler and faster. Use `sync.Map` only when profiling shows contention.

### Cond

A condition variable for goroutines waiting on an event:

```go
var mu sync.Mutex
cond := sync.NewCond(&mu)
ready := false

// waiter
go func() {
    mu.Lock()
    for !ready {
        cond.Wait()
    }
    mu.Unlock()
}()

// signaller
mu.Lock()
ready = true
cond.Broadcast() // or cond.Signal() for one
mu.Unlock()
```


## Summary

`Cond` is rarely needed — channels usually cover the same patterns more clearly.

## Related Posts

- Goroutines and Channels
- Testing in Go


**Tags:** `go` `concurrency` `sync` `synchronization` 

## References

 - [Go blog: Mutexes](https://go.dev/blog/race-detector)
 - [Package sync docs](https://pkg.go.dev/sync)

