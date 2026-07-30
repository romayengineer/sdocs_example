# JSON in Go



📌 **Status: published**


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

- Structs and Methods in Go
- Interfaces in Go

Go provides robust JSON encoding and decoding through the `encoding/json` standard library package.

## Marshaling (Encoding)

Convert Go values to JSON using `json.Marshal`:

```go
type User struct {
    Name  string `json:"name"`
    Email string `json:"email"`
    Age   int    `json:"age,omitempty"`
}

u := User{Name: "Alice", Email: "alice@example.com", Age: 30}
data, _ := json.Marshal(u)
fmt.Println(string(data))
// {"name":"Alice","email":"alice@example.com","age":30}
```

## Unmarshaling (Decoding)

Parse JSON into Go structs:

```go
jsonStr := `{"name":"Bob","email":"bob@example.com"}`
var u User
err := json.Unmarshal([]byte(jsonStr), &u)
if err != nil {
    log.Fatal(err)
}
fmt.Println(u.Name) // Bob
```

## Struct Tags

Tags control the JSON field name and behavior:

```go
type Config struct {
    Host    string `json:"host"`                // rename to host
    Port    int    `json:"port,omitempty"`       // omit if zero
    Secret  string `json:"-"`                    // always omit
    Tags    []string `json:"tags,omitempty"`     // omit if nil/empty
}
```

## Streaming with Encoder and Decoder

For large payloads or HTTP streams:

```go
// Encoding to a writer
json.NewEncoder(w).Encode(users)

// Decoding from a reader
var u User
json.NewDecoder(r.Body).Decode(&u)
```

## Unstructured JSON

Use `map[string]any` and `[]any` for dynamic data:

```go
var data map[string]any
json.Unmarshal([]byte(jsonStr), &data)
name := data["name"].(string)
```

## Custom Marshaling

Implement `json.Marshaler` and `json.Unmarshaler` for custom types:

```go
type Color struct{ R, G, B uint8 }

func (c Color) MarshalJSON() ([]byte, error) {
    return json.Marshal(fmt.Sprintf("#%02x%02x%02x", c.R, c.G, c.B))
}

func (c *Color) UnmarshalJSON(data []byte) error {
    var s string
    if err := json.Unmarshal(data, &s); err != nil {
        return err
    }
    _, err := fmt.Sscanf(s, "#%02x%02x%02x", &c.R, &c.G, &c.B)
    return err
}
```

## Performance Tips

- Reuse buffers and encoders in hot paths
- Use `json.RawMessage` for deferring decoding or partial parsing
- Prefer streaming (`Encoder`/`Decoder`) over marshaling for large data
- Use `strings.Builder` or `bytes.Buffer` for intermediate output

`encoding/json` is the most commonly used package in Go after `fmt`. For performance-critical applications, alternatives like `json-iterator/go` or `segmentio/encoding/json` offer significant speedups.



## Related Posts

- Building HTTP Servers and Clients in Go
- Structs and Methods in Go


**Tags:** `go` `json` `serialization` `encoding` 


## References

 - [Go blog: JSON and Go](https://go.dev/blog/json)
 - [Go encoding/json docs](https://pkg.go.dev/encoding/json)

