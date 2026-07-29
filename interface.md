1. What is an Interface?

An interface is a collection of method signatures.

It does not contain implementations.

type Animal interface {
    Speak()
    Eat()
}

2. Why do we use Interfaces?

Interfaces provide abstraction.

Instead of writing code for a specific type, we write code for a behavior.

Instead of depending on

Razorpay

we depend on

Payment

This provides

Loose Coupling
Flexibility
Reusability
Easy Testing (Mocking)


3. Implicit Interface Implementation

Go does NOT have

implements

or

implements

keyword.

If a struct implements all methods of an interface,

Go automatically says

This struct satisfies the interface.

Example

type Animal interface {
    Speak()
}

type Dog struct{}

func (d Dog) Speak() {
    fmt.Println("Woof")
}

Dog automatically implements Animal.


4. Method Set Rule

A type satisfies an interface only if it implements ALL methods.

Example

type Animal interface {
    Speak()
    Eat()
}

Dog

func (d Dog) Speak(){}

Dog does NOT implement Animal because

Eat()

is missing.


5. Interface Variable

var a Animal

Interface internally stores

Dynamic Type
Dynamic Value

Initially

Dynamic Type  : nil
Dynamic Value : nil

Therefore

a == nil

is

true

Calling

a.Speak()

causes a runtime panic because there is no concrete value behind the interface.

6. Assigning a Struct to an Interface
var a Animal = Dog{}

Internally

Dynamic Type  : Dog
Dynamic Value : Dog{}

Now

a.Speak()

calls

Dog.Speak()


7. Dynamic Dispatch

Example

animals := []Animal{
    Dog{},
    Cat{},
}

Loop

for _, a := range animals {
    a.Speak()
}

Go checks

Dynamic Type

If

Dog

↓

Calls

Dog.Speak()

If

Cat

↓

Calls

Cat.Speak()

This is called

Dynamic Dispatch


8. Interface vs Struct

Struct

Stores Data
Contains Fields
Contains Methods

Example

type User struct {
    Name string
}

Interface

Stores NO data
Only declares behavior

Example

type Reader interface {
    Read()
}

9. Pointer Receiver vs Value Receiver
Value Receiver
func (d Dog) Speak(){}

Works

Dog{}

✔

Works

&Dog{}

✔

Pointer Receiver
func (d *Dog) Speak(){}

Works

&Dog{}

✔

Does NOT work

Dog{}

❌

Reason

Go checks the method set.

Pointer receiver belongs to

*Dog

not

Dog
10. Real Backend Example

Instead of

ProcessRazorpay()

ProcessStripe()

ProcessPaypal()

Write

type Payment interface {
    Pay(amount int) error
}

func ProcessPayment(p Payment) error {
    return p.Pay(100)
}

Tomorrow

PhonePe

comes.

Just implement

Pay()