

Go Memory Management:
        Memory Managemnt is a proccess of managing how memory is allocated to the data,where it is being allocated,how long should remain in the memory and when unused memory should be reclaimed.

Escape Analysis:

    Escape Analysis is compliler optimisation in go where it decides whether the varibles/data needs to be allocated in the stack or it escapes to the heap

Stack Memory:
    Stores temporary data associated with function execution.
    The memory used by a function's stack frame is no longer needed
    once the function returns.

    func add() int {
    x := 10
    y := 20
    return x + y
            }

Heap Memory:
    Stores data that may need to remain accessible beyond the
    current function execution.
    Unused heap objects are eventually reclaimed by the Garbage Collector.

    func create() *int {
    x := 10
    return &x
}


Garbage Collection:
    Garbage Collection is a runtime process where it automatically recalims the unused memory in the heap 

   Go uses a concurrent, tri-color, mark-and-sweep garbage collector. During application goroutine execution, the GC can perform its work concurrently with the application. During the marking phase, it conceptually categorizes objects into three states: white, grey, and black. White objects are not processed yet, grey objects are discovered but their references have not been fully scanned, and black objects and their references have been completely scanned. The GC starts from the roots and identifies reachable objects during the mark phase. Then, during the sweep phase, it identifies unreachable objects and reclaims the memory occupied by them so that the memory can be reused.


   Tri-Colour -- It is a marking mechanism
   Mark-Sweep --- It is a overall collection approach

   GC Pressure:
    so GC refers to the amount of work imposed on the GC due to excessive or frequent memory allocations,especially short lived heap alloactions



    Memory Leaks in GO:

        A Memory Leak in go occurs when memory that is no longer logically needed by the application remains reachable through the refrences preventing the garbage collector from reclaiming it 

        (objects anni refernce iiyi undadam valla gc a objetcs kaavali ani anukuntundhi andu valla vaatini clean teeseyadhu so ikkada memroy leak abuthdhi)

     Memory leak ≠ memory that GC failed to clean.

     Example :
     var cache = make(map[string][]byte)

func addData(key string) {
    cache[key] = make([]byte, 1024*1024)
}

Suppose thousands of keys add chestunnam:

cache
  |
  ├── key1 → 1 MB
  ├── key2 → 1 MB
  ├── key3 → 1 MB
  ├── key4 → 1 MB
  └── ...

Application ki key1, key2 etc. inka avasaram lekapoyina map lo references remain avuthunnayi.

So GC:

"Ee objects still reachable from global cache."

ani anukuntundi.

Therefore reclaim cheyyadu.



Common Interview Examples:
1. Growing global maps/caches
2. Goroutine leaks
3. Unbounded slices
4. Not releasing resources properly
5. Long-lived references keeping large objects alive



