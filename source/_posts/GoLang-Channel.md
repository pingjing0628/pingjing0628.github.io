---
title: GoLang-Channel
date: 2020-09-10 12:26:02
tags:
  - GoLang
  - Goroutine
  - Thread
  - Channel
categories: 
  - GoLang
---

# Channel

<!--more-->
---

Channel is a pipeline, push and pull the value.
Channel will wait for another task finished and keep going. => this can make each `goroutine` work concurrently

### Create Channel
`ch := make(chan int)`

### Push into channel
`ch <- v`

### Pull
`v := <-ch`

## Channel Block
2 conditions make channel block:
1. Push into channel, but when other goroutine haven't pull out, it will be forced to wait other finish.
2. When channel is empty, the `goroutine` which want to pull, it will be forced to wait other `goroutine` to pull and push

### The waiting case of Goroutine push into channel

```golang=
func main() {
	ch := make(chan string)

	go func() { // calculate goroutine
		fmt.Println("Starts calculating")
		time.Sleep(time.Second)
		fmt.Println("Ends calculating")

		ch <- "END" // goroutine will be forced to wait

		fmt.Println("calculate goroutine finished")
	}()

	time.Sleep(2 * time.Second) // make main is slower than goroutine
	fmt.Println(<-ch)
	time.Sleep(time.Second)
	fmt.Println("main goroutine finished")
}
```

```
Starts calculating            
Ends calculating
calculate goroutine finished  
END                           
main goroutine finished      
```

* Use time.Sleep to force main slower than calculate
* Put END into channel
* But main haven't pull out data in Channel, so calculate will be forced to wait, so" main goroutine finished " isn't show immediately
* Main pull out data in Channel
* Main finished

### The waiting case of Goroutine pull out from channel
```golang=
func main()  {
	ch := make(chan string)

	go func() {
		fmt.Println("Starts calculating")
		time.Sleep(time.Second)   // Heavy calculation
		fmt.Println("Ends calculating")

		ch <- "ENDS"

		fmt.Println("calculate goroutine finished")
	}()

	fmt.Println("main goroutine is waiting for channel to receive value")
	fmt.Println(<-ch) // goroutine will be forced to wait
	fmt.Println("main goroutine finished")
}
```

```
main goroutine is waiting for channel to receive value 
Starts calculating
Ends calculating
calculate goroutine finished
ENDS                                                    
main goroutine finished
```

* Calculate haven't pushed into Channel when main pulled out, it will forced main to wait.
* The last line of main isn't show up immediately
* Put END into channel
* Main pull out data in Channel
* main finished

## Unbuffered Channel
* Push a data will cause push side waiting
* Pull out without data will cause pull side waiting

## Buffered Channel
`ch := make(chan int, 100)`
The second variable will define buffer length, it will only be blocked cause to wait when data is full in buffer.

```golang=
// unbuffered
func main()  {
	ch := make(chan int)

	ch <- 1
	fmt.Println(<-ch)
}
```

```
fatal error: all goroutines are asleep - deadlock!

goroutine 1 [chan send]:
main.main()
        D:/go/goChannel_goRoutine/test.go:8 +0x49
exit status 2

```

1. only one goroutine: main
2. Without other goroutine pull out data in Channel after push "1", so it block!
3. Because main is already blocked when push into data, so it will never execute pull out and cause to deadlock.

```golang=
// buffered
func main()  {
	ch := make(chan int, 1)
	ch <- 1
	fmt.Println(<-ch)
}
```

```
1
```
1. After pushed into channel and it didn't over buffer length, so it won't be blocked.

## Channel in Loop


## Channel in range


## Avoid waiting by using select

