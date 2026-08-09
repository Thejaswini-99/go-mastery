# Go Interfaces — Notes

---

## 1. What is an Interface?

An interface is a collection of method signatures. It does not contain implementations.

```go
type Animal interface {
    Speak()
    Eat()
}
```

---

## 2. Why do we use Interfaces?

Interfaces provide **abstraction** — instead of writing code for a specific type, we write code for a behavior.

Instead of depending on `Razorpay`, we depend on `Payment`.

This provides:
- Loose Coupling
- Flexibility
- Reusability
- Easy Testing (Mocking)

---

## 3. Implicit Interface Implementation

Go does NOT have an `implements` keyword.

If a struct implements all methods of an interface, Go automatically considers it as satisfying the interface.

```go
type Animal interface {
    Speak()
}

type Dog struct{}

func (d Dog) Speak() {
    fmt.Println("Woof")
}

// Dog automatically implements Animal ✅
```

---

## 4. Method Set Rule

A type satisfies an interface only if it implements ALL methods.

```go
type Animal interface {
    Speak()
    Eat()
}

// Dog only implements Speak()
func (d Dog) Speak() {}

// Dog does NOT implement Animal ❌ → Eat() is missing!
```

---

## 5. Interface Variable

```go
var a Animal
```

An interface internally stores two things:
```
Dynamic Type  : nil  (which struct is behind it)
Dynamic Value : nil  (the actual value)
```

Since both are nil → `a == nil` is `true`

Calling `a.Speak()` causes a **runtime panic** because there is no concrete value behind the interface.

---

## 6. Assigning a Struct to an Interface

```go
var a Animal = Dog{}
```

Now internally:
```
Dynamic Type  : Dog
Dynamic Value : Dog{}
```

Now `a.Speak()` calls `Dog.Speak()` ✅

---

## 7. Dynamic Dispatch

Go checks the dynamic type at runtime and calls the correct method.

```go
animals := []Animal{
    Dog{},
    Cat{},
}

for _, a := range animals {
    a.Speak()  // Go checks dynamic type and calls correct Speak()
}
```

```
a is Dog → calls Dog.Speak() → "Woof"
a is Cat → calls Cat.Speak() → "Meow"
```

This is called **Dynamic Dispatch** — the method called depends on the actual type at runtime.

---

## 8. Interface vs Struct

| | Struct | Interface |
|---|---|---|
| Stores | Data (fields) | Nothing |
| Contains | Fields + Methods | Only method signatures |
| Example | `type User struct { Name string }` | `type Reader interface { Read() }` |

---

## 9. Pointer Receiver vs Value Receiver with Interfaces

**Value Receiver:**
```go
func (d Dog) Speak() {}
```
| Works with | Result |
|---|---|
| `Dog{}` | ✅ |
| `&Dog{}` | ✅ |

**Pointer Receiver:**
```go
func (d *Dog) Speak() {}
```
| Works with | Result |
|---|---|
| `&Dog{}` | ✅ |
| `Dog{}` | ❌ |

**Why?** Go checks the method set. Pointer receiver belongs to `*Dog` not `Dog`.

> Rule: if you use pointer receiver → always pass `&Dog{}` to the interface variable!

---

## 10. Real Backend Example

**Without interface (bad):**
```go
ProcessRazorpay()
ProcessStripe()
ProcessPaypal()
// add new payment → change existing code ❌
```

**With interface (good):**
```go
type Payment interface {
    Pay(amount int) error
}

func ProcessPayment(p Payment) error {
    return p.Pay(100)
}

// Tomorrow PhonePe comes → just implement Pay() ✅
type PhonePe struct{}
func (p PhonePe) Pay(amount int) error {
    // PhonePe logic here
    return nil
}
```

No changes to `ProcessPayment` needed! This is the power of interfaces. 💪

---

## Summary

```
Interface   → defines WHAT to do (behavior)
Struct      → defines HOW to do it (implementation)
Implicit    → no implements keyword, Go auto-detects
Dynamic     → correct method called at runtime
Pointer     → use &Dog{} with pointer receivers
Backend     → depend on interface not concrete type
```