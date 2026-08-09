# Go Pointers — Notes

## What is a pointer?
- A variable stores a **value**
- A pointer stores the **address** of a variable

```go
x := 10
p := &x       // p stores address of x
fmt.Println(x)   // 10
fmt.Println(p)   // 0xc000018050 (hex address)
fmt.Println(*p)  // 10 (value at that address)
```

---

## Operators
| Operator | Meaning |
|---|---|
| `&x` | address of x |
| `*p` | value at address p |
| `*p = val` | change value at address p |

---

## Changing value through pointer

```go
x := 10
p := &x
*p = 20

fmt.Println(x)   // 20 ← changed!
fmt.Println(*p)  // 20
```

Because `p` points to `x`'s address — changing `*p` changes `x` directly.

---

## Pointers with functions

### Without pointer (copy is passed)
```go
func addTen(n int) {
    n = n + 10   // changes local copy only
}

func main() {
    x := 5
    addTen(x)
    fmt.Println(x)  // still 5!
}
```

### With pointer (address is passed)
```go
func addTen(n *int) {
    *n = *n + 10   // changes original!
}

func main() {
    x := 5
    addTen(&x)
    fmt.Println(x)  // 15 ✅
}
```

> Rule: without pointer → function gets a COPY → original unchanged
> Rule: with pointer → function gets ADDRESS → original changed

---

## Pointers with structs

```go
type Person struct {
    name string
    age  int
}
```

### Without pointer (copy)
```go
func birthday(p Person) {
    p.age = p.age + 1  // changes copy only
}

func main() {
    p := Person{name: "Thejas", age: 25}
    birthday(p)
    fmt.Println(p.age)  // still 25!
}
```

### With pointer
```go
func birthday(p *Person) {
    p.age = p.age + 1  // Go auto-dereferences struct pointer!
}

func main() {
    p := Person{name: "Thejas", age: 25}
    birthday(&p)
    fmt.Println(p.age)  // 26 ✅
}
```

> Note: For struct pointers, Go auto-dereferences — use `p.age` not `(*p).age`

---

## Nil pointers

```go
var p *int
fmt.Println(p)   // nil
fmt.Println(*p)  // PANIC! invalid memory address
```

### Always check for nil before dereferencing
```go
if p != nil {
    fmt.Println(*p)  // safe ✅
}
```

---

## new keyword

```go
p := new(int)   // creates pointer to int with zero value
*p = 42
fmt.Println(*p) // 42
```

Same as:
```go
x := 0
p := &x
```

---

## Pointers with slices

### Without pointer (append doesn't affect original)
```go
func addElement(s []int) {
    s = append(s, 100)  // new slice created internally
}

func main() {
    nums := []int{1, 2, 3}
    addElement(nums)
    fmt.Println(nums)  // [1 2 3] unchanged!
}
```

### With pointer
```go
func addElement(s *[]int) {
    *s = append(*s, 100)
}

func main() {
    nums := []int{1, 2, 3}
    addElement(&nums)
    fmt.Println(nums)  // [1 2 3 100] ✅
}
```

---

## Summary

```
&x            → address of x
*p            → value at address p
*p = val      → change value at address p

func f(n *int)     → modify original int
func f(p *Person)  → modify original struct
p.age              → Go auto-dereferences struct pointers

var p *int    → p is nil (zero value for pointers)
*p            → PANIC if p is nil! always check nil first
new(int)      → creates pointer to zero value

pass *[]int        → modify original slice
*s = append(*s)    → correct way to append via pointer
```