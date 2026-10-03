# Go Generics

## Table of Contents

- [1. What are Generics?](#1-what-are-generics)
- [2. Type Parameter](#2-type-parameter)
- [3. Type Argument](#3-type-argument)
- [4. Constraints](#4-constraints)
- [5. any](#5-any)
- [6. Union Constraint: int | float64](#6-union-constraint-int--float64)
- [7. comparable](#7-comparable)
- [8. Generic Struct](#8-generic-struct)
- [9. Generic Struct Methods](#9-generic-struct-methods)
- [10. Underlying Type: ~](#10-underlying-type-)
- [Generics vs any vs Interface](#generics-vs-any-vs-interface)
- [Interview Add-ons](#interview-add-ons)
- [Self-check Questions](#self-check-questions)
- [Interview One-liner](#interview-one-liner)

## 1. What are Generics?

Generics allow us to write reusable, type-safe code that works with multiple data types.

Without generics, we have to write separate functions for the same logic for each type:

```go
func addInt(a int, b int) int {
    return a + b
}

func addFloat(a float64, b float64) float64 {
    return a + b
}
```

With generics:

```go
func add[T int | float64](a T, b T) T {
    return a + b
}
```

## 2. Type Parameter

`T` is called a **type parameter**. It is a placeholder for an actual type.

```go
func add[T int | float64](a T, b T) T
```

Example:

```go
add(10, 20) // T = int
```

## 3. Type Argument

The actual type supplied to the type parameter is called a **type argument**.

```go
add[int](10, 20)
```

| Term | In this example |
|---|---|
| Type parameter | `T` |
| Type argument | `int` |

Usually Go can infer the type automatically:

```go
add(10, 20)
```

This is called **type inference**.

## 4. Constraints

A constraint defines which types are allowed for a type parameter.

```go
func add[T int | float64](a T, b T) T
```

| Type | Allowed for `T`? |
|---|---|
| `int` | ✅ |
| `float64` | ✅ |
| `string` | ❌ |

> **Constraint = rules for the type parameter.**

## 5. any

```go
[T any]
```

`any` means any type can be used: `int`, `float64`, `string`, `bool`, structs, and so on.

`any` is an alias for `interface{}`.

> **Important:** `any` doesn't mean we can perform every operation on `T`. The compiler only allows operations guaranteed by the constraint.

## 6. Union Constraint: int | float64

```go
[T int | float64]
```

`T` can be either `int` or `float64`.

| Type | Allowed? |
|---|---|
| `int` | ✅ |
| `float64` | ✅ |
| `string` | ❌ |
| `bool` | ❌ |

## 7. comparable

`comparable` is a predefined constraint. It allows types that can be compared using `==` and `!=`.

```go
func isEqual[T comparable](a T, b T) bool {
    return a == b
}
```

| Type | Comparable? |
|---|---|
| `int` | ✅ |
| `string` | ✅ |
| `bool` | ✅ |
| `[]int` | ❌ (slices cannot be compared using `==`) |

## 8. Generic Struct

Generics can also be used with structs.

```go
type Stack[T any] struct {
    items []T
}
```

Now we can create `Stack[int]`, `Stack[string]`, `Stack[float64]`:

```go
intStack := Stack[int]{}
stringStack := Stack[string]{}
```

Same structure, different types.

## 9. Generic Struct Methods

```go
type Stack[T any] struct {
    items []T
}

func (s *Stack[T]) Push(value T) {
    s.items = append(s.items, value)
}
```

If we create:

```go
stack := Stack[int]{}
```

then `T = int`, so:

```text
Push(value T)
     ↓
Push(value int)
```

## 10. Underlying Type: ~

```go
type MyInt int
```

`MyInt` is a different named type, but its underlying type is `int`.

| Constraint | Meaning |
|---|---|
| `[T int]` | Exactly `int` |
| `[T ~int]` | `int` and any custom type whose underlying type is `int` |

Both `int` and `MyInt` have the underlying type `int`, so both satisfy `[T ~int]`.

## Generics vs any vs Interface

| | Says | Question it answers |
|---|---|---|
| **Interface** | "I care about what behavior the type provides." | What can you **do**? |
| **Generics** | "I can work with multiple types while preserving type information and type safety." | What **type** can I work with? |
| **any** | "I can accept any type." | Any type is accepted. |

## Interview Add-ons

### Why generics instead of interface{}?

Before Go 1.18, reusable code used `interface{}` with type assertions:

```go
func first(items []interface{}) interface{} {
    return items[0]
}

n := first(values).(int) // panics at runtime if it is not an int
```

With generics the type is checked at compile time and no assertion is needed:

```go
func first[T any](items []T) T {
    return items[0]
}

n := first([]int{1, 2, 3}) // n is an int
```

### cmp.Ordered

`comparable` only allows `==` and `!=`. For `<`, `>`, `<=`, `>=`, use `cmp.Ordered` (integers, floats, strings).

```go
import "cmp"

func Max[T cmp.Ordered](a, b T) T {
    if a > b {
        return a
    }
    return b
}
```

### Custom constraint

A constraint can be declared once as an interface and reused:

```go
type Number interface {
    ~int | ~int64 | ~float64
}

func Sum[T Number](nums []T) T {
    var total T
    for _, n := range nums {
        total += n
    }
    return total
}
```

### Multiple type parameters

```go
func Map[T, U any](s []T, f func(T) U) []U {
    result := make([]U, 0, len(s))
    for _, v := range s {
        result = append(result, f(v))
    }
    return result
}

func Filter[T any](s []T, keep func(T) bool) []T {
    var result []T
    for _, v := range s {
        if keep(v) {
            result = append(result, v)
        }
    }
    return result
}
```

### Limitations

- **Methods cannot have their own type parameters.** Only the type can.

  ```go
  func (s *Stack[T]) Convert[U any]() {} // not allowed
  ```

- **No direct type switch on a type parameter.** Convert to `any` first.

  ```go
  switch any(v).(type) {
  case int:
  case string:
  }
  ```

- **Union constraints cannot be used as ordinary types.**

  ```go
  var x int | string // not allowed
  ```

### Standard library generics

| Package | Examples |
|---|---|
| `slices` | `slices.Contains`, `slices.Sort`, `slices.Index`, `slices.Reverse` |
| `maps` | `maps.Keys`, `maps.Values`, `maps.Clone` |
| `cmp` | `cmp.Ordered`, `cmp.Compare` |

### When to use generics vs interfaces

- **Interface:** behavior differs per type (for example `io.Reader`, `error`).
- **Generics:** logic is the same and only the type differs (containers, `Map` / `Filter`, `Min` / `Max`).

## Self-check Questions

**What is the difference between `any` and `comparable`?**
`any` accepts every type but allows no operators. `comparable` accepts only types that support `==` and `!=`, which is why map keys need it.

**Why can't I write `a + b` with `[T any]`?**
The compiler only allows operations guaranteed by the constraint, and `any` guarantees none. Use a constraint such as `int | float64`.

**What does `~int` add over `int`?**
It also accepts named types whose underlying type is `int`, such as `type MyInt int`.

**Can a method on a generic struct introduce a new type parameter?**
No. Use a top-level generic function instead.

## Interview One-liner

> Generics in Go allow us to write reusable, type-safe functions and data structures that work with multiple types. We use type parameters such as `T`, and constraints define which types are allowed for those parameters.