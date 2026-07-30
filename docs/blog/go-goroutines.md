# Goroutines and Channels

*Date: 2026-07-30*

Goroutines are lightweight threads managed by the Go runtime. They are the foundation of concurrency in Go.

## Starting a Goroutine

Prefix any function call with the `go` keyword:

```go
go myFunction()
```

A goroutine runs concurrently with the caller. The program does not wait for it unless synchronized.

## Channels

Channels are the primary way goroutines communicate:

```go
ch := make(chan int)

// Send
ch <- 42

// Receive
val := <-ch
```

### Buffered vs Unbuffered

- **Unbuffered** (`make(chan T)`) — blocks until both sender and receiver are ready
- **Buffered** (`make(chan T, 5)`) — sends block only when the buffer is full

```go
ch := make(chan string, 2)
ch <- "a"
ch <- "b"
fmt.Println(<-ch) // "a"
```

## The `select` Statement

`select` waits on multiple channel operations:

```go
select {
case msg := <-ch1:
    fmt.Println(msg)
case <-time.After(1 * time.Second):
    fmt.Println("timeout")
}
```

## Pipeline Pattern

A common pattern is connecting goroutines with channels:

```go
func gen(nums ...int) <-chan int {
    out := make(chan int)
    go func() {
        for _, n := range nums {
            out <- n
        }
        close(out)
    }()
    return out
}

func sq(in <-chan int) <-chan int {
    out := make(chan int)
    go func() {
        for n := range in {
            out <- n * n
        }
        close(out)
    }()
    return out
}
```

Usage:
```go
for n := range sq(gen(2, 3, 4)) {
    fmt.Println(n) // 4, 9, 16
}
```

Goroutines are cheap (a few KB each), making it practical to start thousands of them. With channels, Go offers a safe, composable concurrency model built on the mantra: *"Do not communicate by sharing memory; instead, share memory by communicating."*



Tags: go concurrency goroutines channels 

