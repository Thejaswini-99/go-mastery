Functions:
Funtions are the reusable block of code

syntax:
func funcname(parameters)(return value){
    //code
}

Types of function delarations:

func greet(){
//code -- funtion with no parameters
}

func greet(s string){
    //code -- function with parameter
}

func square(n int)int{ --function with return value
    //code
}

func add(left string,right int) []int{ -- function with parameters and return values
    //code
}

func divide(a,b int)(int,error){ -- Multiple return values
    //code
}

Variadic Functions:- (Variable num. of arguments)
----> A Function decalred with variable number of arguments

func sun(nums ...int)int{ -- we can pass n number of integers here
    //code
}

Anonymus Functions:
--> A Functions with no name

add := func(a,b int)int{
    return a+b
}
fmt.Println(add(3,5))

Immediately Invoked Functions:
 
 result:= func(a,b int)int{
    return a+b
 }(3,5) // it is called immediatley here
 fmt.Println(result)

Higher Order Funcitos (Function as a Parameter):
  ---> In this fucntion it self is passed as an parameter

func double(n int) int {
    return n * 2
}

func triple(n int) int {
    return n * 3
}

// takes a function as parameter!
func apply(n int, f func(int) int) int {
    return f(n)
}

// usage
fmt.Println(apply(5, double)) // 10
fmt.Println(apply(5, triple)) // 15


Closure:
A function that remembers the variables from the scope where it was created — even after that scope is gone!
func outer() func() int {
    count := 0          // this variable lives in outer

    return func() int { // this inner function is the CLOSURE
        count++         // it remembers count from outer!
        return count
    }
}

func main() {
    counter := outer()  // outer() finished executing
                        // but count is still remembered!

    fmt.Println(counter()) // 1
    fmt.Println(counter()) // 2
    fmt.Println(counter()) // 3
}


Defer : If Defer keyword is used that particular satetment or funciton will be executed after the end or the completion of all the stetments in the end..defer schedules a function call to execute just before the surrounding function returns.



Functions vs Method 

Method : It usually attached with the struct 
Fucntion : It is a standalone function 


// just a function, not attached to anything
func add(a, b int) int {
    return a + b
}

// call it directly
add(3, 5)



Method:
// struct (type)
type Rectangle struct {
    width  float64
    height float64
}

// method — attached to Rectangle
func (r Rectangle) area() float64 {
    return r.width * r.height
}

// call it via the struct
rect := Rectangle{width: 5, height: 3}
fmt.Println(rect.area()) // 15


Function Receivers

Value Receiver
func (p Person) Change(){}

Pointer Receiver
func (p *Person) Change(){}

Value Receiver   = copy   = pass by value
Pointer Receiver = original = pass by reference

Pass by Value vs Value Receiver
// pass by value — FUNCTION
func double(n int) {
    n = n * 2      // copy modified
    fmt.Println(n) // 10
}

n := 5
double(n)
fmt.Println(n) // 5 ← original unchanged!

// value receiver — METHOD
func (r Rectangle) scale() {
    r.width = r.width * 2  // copy modified
}

rect := Rectangle{width: 5}
rect.scale()
fmt.Println(rect.width) // 5 ← original unchanged!



Pass by Reference vs Pointer Receiver
// pass by reference — FUNCTION
func double(n *int) {
    *n = *n * 2    // original modified
}

n := 5
double(&n)
fmt.Println(n) // 10 ← original changed! 

// pointer receiver — METHOD
func (r *Rectangle) scale() {
    r.width = r.width * 2  // original modified
}

rect := Rectangle{width: 5}
rect.scale()
fmt.Println(rect.width) // 10 ← original changed! 