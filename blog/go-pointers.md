# Pointers in Go



📌 **Status: published**


| | |
|---|---|
| **Date** | 2026-07-30 |
| **Last updated** | 2026-07-30 |
| **Category** | tutorial |
| **Difficulty** | intermediate |
| **Reading time** | 5 min |
| **Word count** | ~720 |
| **Author** | Structured Docs |
| **Language** | Go |
| **Version** | Go 1.21 |
| **License** | CC-BY-4.0 |






## Prerequisites

- Structs and Methods in Go
- Basic Go syntax

Go has pointers but no pointer arithmetic. A pointer holds the memory address of a value.

## Basic Syntax

```go
var x int = 42
var p *int = &x  // p points to x

fmt.Println(*p) // 42 (dereference)
*p = 21         // modify x through p
fmt.Println(x)  // 21
```

## Zero Value

The zero value of a pointer is `nil`:

```go
var p *int // nil
if p != nil {
    fmt.Println(*p)
}
```

## Pointers and Functions

Pointers allow functions to modify their arguments:

```go
func increment(n *int) {
    *n++
}

x := 1
increment(&x)
fmt.Println(x) // 2
```

## Pointer to Struct

Struct pointers are commonly used to avoid copying:

```go
type User struct {
    Name string
    Age  int
}

u := &User{Name: "Alice", Age: 30}
u.Name = "Bob" // implicit dereference
```

The dot notation works automatically with pointers to structs (no `->` needed).

## Stack vs Heap

Go's compiler decides where a variable lives. If a variable escapes the function (e.g., returned), it's allocated on the heap:

```go
func newUser() *User {
    u := User{Name: "Alice"} // escapes to heap
    return &u
}
```

This is determined by escape analysis, not by the programmer.

## When to Use Pointers

- Modifying a receiver in a method
- Avoiding large struct copies
- Indicating optional fields (nil means absent)
- Sharing state (use channels for concurrency)

## When NOT to Use

- For small values (int, bool) — pass by value is fine
- When a nil receiver is not meaningful
- In maps and slices — they already reference underlying data

Go's pointers are simple and safe — no pointer arithmetic, no dangling references, and a garbage collector handles cleanup.



## Related Posts

- Structs and Methods in Go
- Slices and Arrays in Go


**Tags:** `go` `pointers` `memory` 


## References

 - [Go blog: Pointers](https://go.dev/doc/faq#Pointers)
 - [Go by example: Pointers](https://gobyexample.com/pointers)

