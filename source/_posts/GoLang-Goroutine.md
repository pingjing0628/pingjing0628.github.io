---
title: GoLang-Goroutine
date: 2020-09-07 11:55:53
tags: 
  - GoLang
  - Goroutine
  - Thread
  - Channel
  - WaitGroup
categories: 
  - GoLang
---

# Goroutine

<!--more-->
---

Goroutine is like the thread in Go, which makes Go create multitask.
Only calls goroutine in main function when program started, which names <b>main</b> goroutine.
`go a(x, y)`
Function begin with go can make `a` run in another goroutine.
Main goroutine end, other goroutine will be force to close.

## Single Thread
Each lines of program is executed by sequence

```golang=
func main() {
    reply("Ni")
    reply("How")
}

func reply(s string) {
    for i := 0; i < 5; i++ {
        time.Sleep(100 * time.Millisecond)
        fmt.Println(s)
    }
}
```
Output:
```
Ni
Ni
Ni
Ni
How
How
How
How
How
```
![](single_thread.JPG)

## Multiple Thread
Execute goroutine which is the number of CPU in the same time at most.

```golang=
func main() {
    go reply("How")
    reply("Ni")
}
```
Output:
```
How
Ni
Ni
How
Ni
How
Ni
How
Ni
```
![](multiple_thread.JPG)

## Wait
Here comes the problem which needs waiting.
When `main` goroutine closed, others two goroutine will be force to close and lead to error.
Sol: Wait other goroutine end and close main goroutine.
```golang=
func main(){
    go reply("Ni")
    go reply("How")
}
```
It contains 3 goroutine:
1. `main`
2. `go reply("Ni")`
3. `go reply("How")`

<b> Three ways to wait </b>
1. `time.Sleep`: sleep in specific time
2. `sync.WaitGroup`: Wait until specific numbers of `Done()` use
3. Channel block: use <b>wait when received</b> this characteristic to avoid thread keep executing

### time.Sleep
```golang=
    func main() {
        go reply("Ni")
        go reply("How")

        time.Sleep(10 * time.Second)
    }
```
![](timeSleep.JPG)

<b>cons:</b> It doesn't know the goroutine execution time is greater or less than sleep time.

### sync.WaitGroup
```golang=
func main() {
    wg := new(sync.WaitGroup)
    wg.Add(2)

    go reply("Ni", wg)
    go reply("How", wg)

    wg.Wait()
}

func reply(s string, wg *sync.WaitGroup) {
    defer wg.Done()

    for i := 0; i < 5; i++ {
        time.Sleep(100 * time.Millisecond)
        fmt.Println(s)
    }
}
```

Create `WaitGroup` Counter which numbers is same as the goroutine that want to wait.
Put `WaitGroup` to goroutine, using `wg.Done()` to  minus 1 when execution finished.
`wg.Wait()` will wait until counter to 0.

![](sync_waitgroup.JPG)

### Channel
```golang=
func main()  {
	// Build a channel
	ch := make(chan string)

	go reply("Ni", ch)
	go reply("How", ch)

	// Read channel out
	<-ch
    <-ch
}

func reply(s string,c chan string) {
	for i := 0; i < 5; i++ {
		time.Sleep(100 * time.Millisecond)
		fmt.Println(s)
	}

	// put END into c
	c <- "END"
}
```
```
How
Ni
Ni
How
Ni
How
How
Ni
Ni
How
```

With 2 goroutine, need to wait 2 `End` push into channel to end main goroutine.

![](channel.png)


## Race Condition
### Multi-Thread Shared Variables

```golang=
func main()  {
	total := 0

	for i := 0; i < 1000; i++ {
		go func() {
			total++
		}()
	}

	time.Sleep(time.Second)

	fmt.Println(total)
}
```

![](mutipleThread_sharedVar.JPG)

Ex: Current in 60, while in multi-thread
1. `goroutine1` get 60 to add
2. `goroutine2` probably got `total` value before `goroutine1` do `total++`
3. In this condition, the result will be 61, not 62

Doing the addition in the same `total` variable in multiple goroutine will lead to operation error because it cannot make sure the secure of value when assign it. => <b>Race Condition</b>

There are 2 solutions:
1. Sync.Mutex
2. Channel

### Sync.Mutex (互斥鎖)

```golang=
type SafeNumber struct {
	v int
	mux sync.Mutex
}

func main()  {
	total := SafeNumber{v: 0}

	for i := 0; i < 1000; i++ {
		go func() {
			total.mux.Lock()
			total.v++
			total.mux.Unlock()
		}()
	}

	time.Sleep(time.Second)
	total.mux.Lock()
	fmt.Println(total.v)
	total.mux.Unlock()
}

```

```
1000
```

![](sync_mutex.JPG)

### Via Channel - security of variable

```golang=
func main()  {
	total := 0
	ch := make(chan int, 1)

	ch <- total

	for i := 0; i < 1000; i++ {
		go func() {
			ch <- <-ch + 1
		}()
	}

	time.Sleep(time.Second)
	fmt.Println(<-ch)
}
```

```
1000
```

![](channel_var.JPG)

1. When `goroutine1` pull out, and there is nothing in Channel
2. Because there is nothing in Channel, cause `goroutine2` waiting
3. When `goroutine1` finished, `total` pushed to Channel
4. When `goroutine2` find out there is something in Channel, pull out and wait to operate.

### REFERENCES
* [Source](https://peterhpchen.github.io/2020/03/08/goroutine-and-channel.html)