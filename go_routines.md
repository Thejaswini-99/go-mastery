
****Main is also a one of the go routine so go runtime starts it automatically while starting the program ****

Sequential Processing:
---
So it is like running multiple tasks in an liner order so second task wont be executed until the first task was first task was done..so multiple tasks would be running on the single core
--- 

Concurrent Processing:
---
It is like running the mutilpe tasks at the same time by switching jobs between them via some time quatum.It is like running the multiple tasks on the single core
---

Parllel Processing:
---
It is like running the differnt tasks on the multiple cores here swithcing of the tasks wont happen
---

| Concept        | Simple meaning                                                            |
| -------------- | ------------------------------------------------------------------------- |
| **Sequential** | One task finishes → next task starts                                      |
| **Concurrent** | Multiple tasks are making progress, possibly by switching                 |
| **Parallel**   | Multiple tasks are literally executing at the same time on multiple cores |



Go routines:

---
Go routines are the light weight threads that runs concurrently and have their spearate independent executions and they were entirely managed by the go runtime scheduler.

---
1.So we uses the go keyword to call the go routines 
2.The order of execution doesn't needs to be the same as the sceduler executes the tasks based on the go routines shared in the LRQ(local runtime queues)

3.There is something called local runtime queue (LRQ) and global runtime queue (GRQ) where the tasks were queued and go runtime scheduler fetches the tasks from the GRQ and places them in LRQ of the os threads and one more thing if there are multiple go routines which requries the os threads go runtime reqs the os kernal who creates them and os scheduler delas or controls them indirectly go routines also responsible for creating the threads

4.Cooperative scheduler: asks/depends on the running task to give up control before another task can run.
Preemptive scheduler: scheduler can interrupt the running task and switch to another task without waiting for it to voluntarily give up control
And for modern Go, remember: Go supports preemption

5.mXn scheduling:
        so here 'M' is the go routines and 'N' is the os threads so go scheduler multiplexes the M gorutines on to the fewer N os threads 

---

Generally the main progran exits before the execution of the go routines there are many ways to stop the main program to exit before the ro routines execution
1.use time.sleep function ---> this was not a correct way to make the main function sleep in the mean while go routines runs

so we use somthing called wait groups from the sync package 

WaitGroups: Wait group is a synchronization pacakge that allows multiple go routines wait for eachother

wg.Add()---it is like a counter for the go routines to run
wg.Done() -- so it is like a decrement to the counter so if a go rouintes implement this decreses by 1 eachtime
wg.Wait()-- this is the wait function like it make sures the go routines were all executed 

----

Channels:

----Do not communicate by sharing memory-instead share memory by communicating----

so channels are a kind of a communitcation pipe between two go routines for sharing the data and also two avoid race condtion we use channels for example oka varibale ni two go routines read chestunnatha varaku parledu if two go routines trying to modify it then it causes a situtaion called race condtion where the variable is passed to channel so here we are communication happens via memory and we can avoid that race condtion 

ch := make(chan int) -- intialising the channel

ch<-10 -- sending the data

var x:= <-ch -- receving the data

channels are two types :

1.Unbufferd Channel:= so by default channel is unbufferd which means capcatiy is 0 so in this sender will wait unitl receiver is ready and vice versa reciver will wait until sender sends the data
ch := make(chan int)

2.Bufferd Channel:= 
ch := make(chan int,5)

 so in this reciver and sender dont wait to if the sender is ready or not receiver ready or not the values was passed to the channel for the capacity so i mean to say this values was passed in to slots and then those values were taken in to the variables for our purposes


closing the channel:
    so closing the channel means i wont send the values it doesnt mean destroying the channel and emptying the channel or stopping the reciver
    close(ch)

x,ok:= <- ch
if !ok{
    print (channel was closed dude)
}

if range is used in the channels it means recive the values one-by-one and if the channel was close dont recive tht valu

for valu :=range(ch){
fmt.Println(value)
}


select:

so select is used to check which  channel is ready to send the data and it executes that channel receiver and go rutime decides which channel is ready to send if both channels are ready they selects one of the channel by pseudo-random selection
and there is default case where if none of the channels didnt receive the data the default case gets executed 

select{
    case result1:= <- val
        fmt.Println(result1) 
    case result2:= <- val
        fmt.Println(result2) 
    default:
        fmt.Println("Defalut valu")

}
so range hides the reciver (<-) and fetches the data internally


DeadLock:
    A deadlock occurs when goroutines are permanently waiting for an event, resource, or communication that cannot happen, so none of them can make progress.
Usually program gets:

fatal error: all goroutines are asleep - deadlock!
    