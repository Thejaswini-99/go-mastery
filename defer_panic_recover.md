

Defer:
---> So if we use defer keyword all the surrounding functions will get executed first later the statement where the defer was passed
----> It is like that staement is passed in to the stack(LIFO)
-----> So if we use continoues 3 defer prinltns statements the first statement will get executed last and last statement will get executed first
KeyWord: defer


Panic :
The panic is a builtin-fucntion used to immediately stop the execution of a program.So when panic occurs it immediatley executes any defered statements (unwinding the stacktrace) and then crashes with an error message.

 panic("it is an panic mode")



Recover:
While panic is used to terminate a program, recover is used to regain control after a panic has occurred. This allows the program to handle the error gracefully instead of crashing.

The recover function is used inside a deferred function to catch the panic message and resume normal execution. If recover is called outside of a deferred function, it has no effect.


a:=recover()

### When to Use defer, panic, and recover:  
**defer: Use defer for cleanup tasks, such as closing files, releasing locks, or logging. It ensures that resources are properly managed, even if an error occurs.
**panic: Use panic for unrecoverable errors, such as invalid input or system failures, where continuing execution would cause more harm.
**recover: Use recover to handle panics gracefully, especially in long-running applications like servers, where crashing is not an option.



### In Go, once a panic happens, the remaining statements in that same function never run. Recovery does not resume execution after the panic line.