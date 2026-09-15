
Worker Pool:
A Worker Pool is a concurrency pattern where a fixed number of worker goroutines continuously consume jobs from a shared jobs channel and process them, instead of creating a new goroutine for every job


Example:

100 jobs
   ↓
jobs channel
   ↓
W1   W2   W3
↓    ↓    ↓
process jobs

Key point: Fixed number of workers, many jobs. This helps control concurrency and avoid creating too many goroutines.


Fan-Out:
Fan-out is a concurrency pattern where work from a single source is distributed across multiple goroutines or workers for parallel processing.
Fan-In:
Fan-in is a concurrency pattern where results from multiple goroutines or channels are combined into a single channel.



Channel Traps:

1. What happens if you send to a closed channel?

Sending to a closed channel causes a panic.

close(ch)
ch <- 10 // panic
2. What happens if you receive from a closed channel?

Receiving from a closed channel is safe. If buffered values are still present, they are received first. After all values are consumed, the receive returns the channel's zero value with ok == false.

value, ok := <-ch

Example:

10, true
20, true
0, false
3. What happens if you send to a nil channel?

The send blocks forever.

var ch chan int
ch <- 10 // blocks forever
4. What happens if you receive from a nil channel?

The receive also blocks forever.

var ch chan int
value := <-ch // blocks forever
5. What happens if you close a nil channel?

It causes a panic.

var ch chan int
close(ch) // panic
6. What happens if you close an already closed channel?

It causes a panic.

close(ch)
close(ch) // panic
7. Does closing a channel destroy it?

No. Closing a channel only indicates that no more values will be sent. Receivers can still receive remaining buffered values and then get zero value with ok == false.

8. Buffered vs Unbuffered trap

Unbuffered:

ch := make(chan int)
ch <- 10

The send blocks until another goroutine receives it.

Buffered:

ch := make(chan int, 2)
ch <- 10
ch <- 20

These sends don't block because the buffer has capacity 2. A third send blocks until someone receives.

One-line interview summary

Closed channel: receive is safe, send panics. Nil channel: both send and receive block forever. Closing nil or an already closed channel causes panic.