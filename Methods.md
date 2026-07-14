
Methods:-
Methods are usually attached to the receviers of some type 

Method vs Fucntions:
Functions are standalone reusable piece of code.This can be called directly
Methods are typically attached to the structs.This can be called via receivers like recivers.methodname

Receiver:
Receiver is a variable that defines which type this method belongs to and gives you access to that types data inside the method
func (r Stundent)Calculatefee(){
    ---in this r is reciver
}

ValueReceiver:-
Value receiver gets a copy of the struct — changes inside the method don't affect the original.It is used when you just want to read the data with no intention of modifying


Pointer Receiver:-
Pointer Receiver is like instead of just copying the data it passes the address of the original struct so any changes reflect 
directly to the struct

type student struct{
    Name string
    Email string
    Fee int
}
func (r *Stundent)Calculatefee(){
    ---in this r is pointer reciver
}


Methods on Different Types:
We can define methods not just on structs but on any type defined in our package.

// int ,string float
 
type Celsius float64
func (c Celsius) ToFahrenheit() float64 {
    return ...
}

// custom slice type
type Names []string      
func (n Names) PrintAll() {
    for _, name := range n {
        fmt.Println(name)
    }
}

we can define methods on maps too


***** Methods can return the values using the reciver
func (s Student) GetFee() int {
    return s.Fee
}

Exported vs Unexported Methods:-
So Exported methods are the pubic methods and can be defined with capital letter at start of method name.UnExported Methods are private methods and can be defined with small letter at the start of method name.


