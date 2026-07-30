# Building HTTP Servers and Clients in Go

📌 **Status: published**

| | |
|---|---|
| **Date** | 2026-07-30 |
| **Last updated** | 2026-07-30 |
| **Category** | tutorial |
| **Difficulty** | intermediate |
| **Reading time** | 7 min |
| **Word count** | ~1000 |
| **Author** | Structured Docs |
| **Language** | Go |
| **Version** | Go 1.22 |
| **License** | CC-BY-4.0 |


## Prerequisites

- Interfaces in Go
- Error Handling in Go

Go's standard library `net/http` provides a powerful HTTP client and server with no external dependencies.

## HTTP Server

### Basic Handler

```go
func helloHandler(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello, %s!", r.URL.Path[1:])
}

func main() {
    http.HandleFunc("/", helloHandler)
    log.Fatal(http.ListenAndServe(":8080", nil))
}
```

### Using the DefaultServeMux

```go
http.HandleFunc("/api/users", func(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    json.NewEncoder(w).Encode(users)
})
```

### Custom Server

```go
srv := &http.Server{
    Addr:         ":8080",
    ReadTimeout:  10 * time.Second,
    WriteTimeout: 10 * time.Second,
    Handler:      myRouter,
}
srv.ListenAndServe()
```

### HTTP Methods and Routing

```go
func userHandler(w http.ResponseWriter, r *http.Request) {
    switch r.Method {
    case http.MethodGet:
        // GET /users/{id}
        id := r.PathValue("id")
        // serve user
    case http.MethodPost:
        // POST /users
        var u User
        json.NewDecoder(r.Body).Decode(&u)
        // create user
    case http.MethodDelete:
        // DELETE /users/{id}
    }
}
```

## Middleware Pattern

```go
func loggingMiddleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        log.Printf("%s %s", r.Method, r.URL.Path)
        next.ServeHTTP(w, r)
    })
}

// Use it
handler = loggingMiddleware(handler)
```

## HTTP Client

```go
resp, err := http.Get("https://api.example.com/data")
if err != nil {
    log.Fatal(err)
}
defer resp.Body.Close()

body, err := io.ReadAll(resp.Body)
fmt.Println(string(body))
```

### Custom Client

```go
client := &http.Client{
    Timeout: 30 * time.Second,
    Transport: &http.Transport{
        MaxIdleConns:    100,
        IdleConnTimeout: 90 * time.Second,
    },
}

req, _ := http.NewRequest("GET", url, nil)
req.Header.Set("Authorization", "Bearer "+token)
resp, err := client.Do(req)
```

## Graceful Shutdown

```go
srv := &http.Server{Addr: ":8080"}
go srv.ListenAndServe()

<-sigChan // wait for SIGINT/SIGTERM
ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
defer cancel()
srv.Shutdown(ctx)
```

Go's `net/http` is production-ready and used by major projects. For complex routing, packages like `gorilla/mux` or `chi` add path parameters and middleware chaining on top of the standard `http.Handler`.


## Related Posts

- JSON in Go
- Context in Go


**Tags:** `go` `http` `server` `client` `web` 

## References

 - [Go blog: HTTP server](https://go.dev/doc/articles/wiki/)
 - [Go net/http docs](https://pkg.go.dev/net/http)

