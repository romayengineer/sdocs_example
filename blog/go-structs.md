# Structs and Methods in Go

📌 **Status: published**

| | |
|---|---|
| **Date** | 2026-07-30 |
| **Last updated** | 2026-07-30 |
| **Category** | tutorial |
| **Difficulty** | beginner |
| **Reading time** | 6 min |
| **Word count** | ~900 |
| **Author** | Structured Docs |
| **Language** | Go |
| **Version** | Go 1.21 |
| **License** | CC-BY-4.0 |
| **Series** | Go Fundamentals (Part 2) |


## Prerequisites

- Basic Go syntax and functions


## Overview

Structs are Go's way of grouping related data. Methods add behavior to structs.

## Basic Usage

```go
type User struct {
    Name  string
    Email string
    Age   int
}
```

### Construction

```go
// By position (not recommended — fragile)
u1 := User{"Alice", "alice@example.com", 30}

// By field name (preferred)
u2 := User{
    Name:  "Bob",
    Email: "bob@example.com",
    Age:   25,
}

// Zero-value initialization
u3 := User{}
```

### Methods

Methods are functions with a **receiver**:

```go
// Value receiver — operates on a copy
func (u User) Greet() string {
    return fmt.Sprintf("Hi, I'm %s", u.Name)
}

// Pointer receiver — can modify the original
func (u *User) Birthday() {
    u.Age++
}
```

### Value vs Pointer Receiver

| Value receiver | Pointer receiver |
|---|---|
| Cannot modify original | Can modify the struct |
| Works on a copy | Works on the original |
| Good for read-only methods | Good for mutating methods |

Rule of thumb: be consistent. If any method needs a pointer receiver, use pointer receivers for all methods on that type.


## Advanced Topics

Go has composition over inheritance through embedding:

```go
type Admin struct {
    User           // embedded — fields and methods promoted
    Permissions []string
}

a := Admin{
    User: User{Name: "Carol", Email: "carol@example.com", Age: 28},
    Permissions: []string{"read", "write", "delete"},
}

fmt.Println(a.Name)  // promoted from User
fmt.Println(a.Greet()) // promoted method
```

Struct tags provide metadata often used by libraries:

```go
type Config struct {
    Host string `json:"host" yaml:"host"`
    Port int    `json:"port" yaml:"port"`
}
```

Tags are accessed via `reflect`:

```go
t := reflect.TypeOf(Config{})
field, _ := t.FieldByName("Host")
fmt.Println(field.Tag.Get("json")) // "host"
```


## Summary

Structs and methods form the backbone of Go's type system, offering a simple but powerful model for organizing data and behavior.

## Related Posts

- Interfaces in Go
- Goroutines and Channels


**Tags:** `go` `structs` `methods` `types` 

## References

 - [Effective Go: Structs](https://go.dev/doc/effective_go#composite_literals)
 - [Go by example: Structs](https://gobyexample.com/structs)

