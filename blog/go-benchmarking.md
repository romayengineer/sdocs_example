# Benchmarking and Profiling in Go


📌 **Status: published`

| | |
|---|---|
| **Date** | 2026-07-30 |
| **Last updated** | 2026-07-30 |
| **Category** | tutorial |
| **Difficulty** | intermediate |
| **Reading time** | 7 min |
| **Word count** | ~950 |
| **Author** | Structured Docs |
| **Language** | Go |
| **Version** | Go 1.21 |
| **License** | CC-BY-4.0 |

## Prerequisites

- Testing in Go
- Slices and Arrays in Go

## Overview

Go includes built-in support for benchmarking, profiling, and testing through the `testing` package and the `go test` command.

## Basic Usage

### Writing Benchmarks

Benchmark functions are placed in `_test.go` files and follow the `BenchmarkXxx(b *testing.B)` signature:

```go
// sum.go
func Sum(nums []int) int {
    var total int
    for _, n := range nums {
        total += n
    }
    return total
}

// sum_test.go
func BenchmarkSum(b *testing.B) {
    nums := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
    for i := 0; i < b.N; i++ {
        Sum(nums)
    }
}
```

### Running Benchmarks

```sh
go test -bench=. -benchmem
```

Output:
```
BenchmarkSum-8    1000000000    0.42 ns/op    0 B/op    0 allocs/op
```

The `b.N` is automatically adjusted by the framework to get a stable measurement.

### Sub-benchmarks

```go
func BenchmarkSum(b *testing.B) {
    sizes := []int{10, 100, 1000}
    for _, n := range sizes {
        b.Run(fmt.Sprintf("n=%d", n), func(b *testing.B) {
            nums := make([]int, n)
            for i := 0; i < b.N; i++ {
                Sum(nums)
            }
        })
    }
}
```

### Comparing Implementations

```go
func BenchmarkConcat(b *testing.B) {
    b.Run("strings.Builder", func(b *testing.B) {
        for i := 0; i < b.N; i++ {
            var sb strings.Builder
            for j := 0; j < 100; j++ {
                sb.WriteString("a")
            }
            _ = sb.String()
        }
    })
    b.Run("+= operator", func(b *testing.B) {
        for i := 0; i < b.N; i++ {
            s := ""
            for j := 0; j < 100; j++ {
                s += "a"
            }
            _ = s
        }
    })
}
```

## Best Practices

- Disable CPU scaling: `sudo cpupower frequency-set --governor performance`
- Don't let the compiler optimize away the result — assign to a package-level variable
- Use `benchstat` to compare benchmark runs statistically
- Set `-benchtime=10x` for quick checks, `-benchtime=5s` for stable results

```go
var Result int // package-level sink

func BenchmarkSum(b *testing.B) {
    nums := []int{1, 2, 3}
    var r int
    for i := 0; i < b.N; i++ {
        r = Sum(nums)
    }
    Result = r // prevent dead code elimination
}
```

## Advanced Topics

### Profiling

```sh
go test -bench=. -cpuprofile=cpu.prof -memprofile=mem.prof
go tool pprof cpu.prof
```

In the pprof interactive shell:
```
top      # top consuming functions
web      # open flame graph (requires graphviz)
list Sum # show line-by-line breakdown
```

### Allocs and Escape Analysis

```sh
go test -bench=. -benchmem -gcflags="-m -m" 2>&1 | grep "escapes"
```

## Summary

Go's built-in benchmarking and profiling tools make performance analysis a first-class part of the development workflow — no external tools required.

## Related Posts

- Testing in Go
- Race Detector in Go

**Tags:** `go` `benchmarks` `profiling` `performance` 

## References

 - [Go blog: Profiling Go programs](https://go.dev/blog/pprof)
 - [Go testing docs](https://pkg.go.dev/testing)

