# The Race Detector in Go



📌 **Status: published**


| | |
|---|---|
| **Date** | 2026-07-30 |
| **Last updated** | 2026-07-30 |
| **Category** | tutorial |
| **Difficulty** | intermediate |
| **Reading time** | 6 min |
| **Word count** | ~800 |
| **Author** | Structured Docs |
| **Language** | Go |
| **Version** | Go 1.21 |
| **License** | CC-BY-4.0 |






## Prerequisites

- Goroutines and Channels
- Sync Package in Go

Go's built-in race detector helps find data races — concurrent access to shared memory where at least one access is a write.

## What is a Data Race?

A data race occurs when two goroutines access the same variable concurrently and at least one is a write:

```go
var counter int

func main() {
    go func() {
        for i := 0; i < 1000; i++ {
            counter++ // read + write
        }
    }()
    go func() {
        for i := 0; i < 1000; i++ {
            counter++ // read + write
        }
    }()
    time.Sleep(time.Second)
    fmt.Println(counter) // likely not 2000
}
```

## Enabling the Race Detector

```sh
go test -race ./...
go run -race main.go
go build -race -o myapp .
```

## Example Output

```
==================
WARNING: DATA RACE
Write at 0x00c0000... by goroutine 8:
  main.main.func1()
      main.go:10 +0x4a

Previous read at 0x00c0000... by goroutine 7:
  main.main.func2()
      main.go:15 +0x4a

Goroutine 8 (running) created at:
  main.main()
      main.go:9 +0x64
Goroutine 7 (finished) created at:
  main.main()
      main.go:14 +0x64
==================
```

## Fixing Races

### Mutex

```go
var (
    counter int
    mu      sync.Mutex
)

mu.Lock()
counter++
mu.Unlock()
```

### Atomic Operations

```go
var counter atomic.Int64
counter.Add(1)
```

### Channels

```go
ch := make(chan int)
go func() {
    ch <- 1
}()
val := <-ch
```

## Common Race Patterns

### Map access without sync

```go
var m = make(map[string]int)

// RACE: concurrent read and write
go func() { m["key"] = 1 }()
go func() { _ = m["key"] }()
```

Fix: use `sync.RWMutex` or `sync.Map`.

### Slice access

```go
var results []int
var wg sync.WaitGroup
for i := 0; i < 10; i++ {
    wg.Add(1)
    go func(i int) {
        results = append(results, i) // RACE
        wg.Done()
    }(i)
}
wg.Wait()
```

Fix: preallocate or use channel.

## Performance Impact

The race detector adds significant overhead:
- Memory usage: 5-10x
- Execution time: 2-20x slower
- Always test with `-race` in CI, never in production

## CI Integration

```yaml
test:
  script:
    - go test -race -count=1 ./...
```

The `-count=1` flag disables test caching to ensure fresh runs.

## What the Race Detector CANNOT Find

- Deadlocks
- Livelocks
- Incorrect synchronization (e.g., wrong mutex usage that happens to work)
- Races in cgo or assembly code
- Races on non-standard memory (mmap, shared memory)

The race detector is one of Go's killer features. Running it regularly catches real bugs that are notoriously difficult to find through testing alone.



## Related Posts

- Goroutines and Channels
- Sync Package in Go


**Tags:** `go` `race-detector` `concurrency` `debugging` 


## References

 - [Go blog: Race Detector](https://go.dev/blog/race-detector)
 - [Go race detector docs](https://go.dev/doc/articles/race_detector)

