# Error Handling in Go

## 1. Basic Error Handling

In Go, errors are **values** — they are returned explicitly from functions, not thrown like exceptions.

```go
// returning an error
func divide(a, b int) (int, error) {
    if b == 0 {
        return 0, errors.New("can't divide by zero")
    }
    return a / b, nil
}

// checking an error
result, err := divide(10, 0)
if err != nil {
    fmt.Println(err)
    return
}
fmt.Println(result)
```

**Why Go uses `err != nil` instead of try/catch:**
- You know **exactly** which function/line threw the error
- Errors are expected, not exceptional
- No hidden control flow

---

## 2. errors.New

Simplest way to create an error.

```go
import "errors"

errors.New("something went wrong")
```

---

## 3. Custom Errors

`error` in Go is an **interface**:

```go
type error interface {
    Error() string
}
```

So any struct that implements `Error() string` method automatically becomes an error!

### Defining a custom error:

```go
type CustomError struct {
    args int
    msg  string
}

func (c *CustomError) Error() string {
    return fmt.Sprintf("args: %d error is: %s", c.args, c.msg)
}
```

### Returning a custom error:

```go
func divide(a, b int) (int, error) {
    if b == 0 {
        return 0, &CustomError{args: b, msg: "can't divide by zero"}
    }
    return a / b, nil
}
```

**Why `&CustomError` and not `CustomError`?**
Because `Error()` method has a pointer receiver `*CustomError`, so you must pass the address.

### How does `fmt.Println(err)` know to call `Error()`?
`fmt.Println` internally checks if the value satisfies the `error` interface and calls `Error() string` automatically. Interface magic! 🎯

---

## 4. errors.Is

Used to check if the error matches a **specific expected error**.

```go
// declare package level error variable (convention: start with Err)
var ErrDivideByZero = errors.New("can't divide by zero")

func divide(a, b int) (int, error) {
    if b == 0 {
        return 0, ErrDivideByZero
    }
    return a / b, nil
}

func main() {
    result, err := divide(10, 0)
    if err != nil {
        if errors.Is(err, ErrDivideByZero) {
            fmt.Println("caught divide by zero error!")
        }
        return
    }
    fmt.Println(result)
}
```

**When to use what:**
- `err != nil` → you just want to know SOMETHING went wrong
- `errors.Is` → you want to handle SPECIFIC errors differently

---

## 5. errors.As

Used to **extract the custom error struct** so you can access its fields.

```go
func main() {
    var ce *CustomError
    result, err := divide(10, 0)
    if err != nil {
        if errors.As(err, &ce) {
            fmt.Println(ce.args, ce.msg) // access struct fields directly!
        }
        return
    }
    fmt.Println(result)
}
```

**`errors.Is` vs `errors.As`:**

| | errors.Is | errors.As |
|---|---|---|
| Purpose | Check if error matches | Extract the error struct |
| Returns | true/false | actual struct with fields |
| Use when | you want yes/no answer | you want to inspect error details |

---

## 6. Complete Example

```go
package main

import (
    "errors"
    "fmt"
)

// custom error struct
type CustomError struct {
    args int
    msg  string
}

// must implement Error() string to satisfy error interface
func (c *CustomError) Error() string {
    return fmt.Sprintf("args: %d error is: %s", c.args, c.msg)
}

// predefined error
var ErrDivideByZero = errors.New("can't divide by zero")

func divide(a, b int) (int, error) {
    if b == 0 {
        return 0, &CustomError{args: b, msg: "can't divide by zero"}
    }
    return a / b, nil
}

func main() {
    // using err != nil
    result, err := divide(10, 0)
    if err != nil {
        fmt.Println(err) // calls Error() automatically
    }
    fmt.Println(result)

    // using errors.As
    var ce *CustomError
    _, err = divide(10, 0)
    if errors.As(err, &ce) {
        fmt.Println(ce.args, ce.msg)
    }
}
```

---

## 7. Quick Summary

| Concept | Usage |
|---|---|
| `errors.New` | Create a simple error |
| `err != nil` | Check if any error occurred |
| `errors.Is` | Check if error matches expected error |
| `errors.As` | Extract custom error struct to access fields |
| `Error() string` | Must implement to make struct a valid error |

---

## Key Takeaways

- Error is just an **interface** in Go — any struct with `Error() string` is an error
- Errors are **returned**, not thrown — you always know where they come from
- Use **custom errors** when you need more context (error codes, arguments, etc.)
- Use **package level `var Err...`** for predefined sentinel errors
- `fmt.Println(err)` automatically calls `Error()` method via interface