---
title: go面试题
date: 2023-09-04 18:46:00
tags: golang
id: 1693824394
---
## Golang 逃逸分析
[参考](https://zhuanlan.zhihu.com/p/419471944)
go build 或 run 时增加 -gcflags="-m "
```
package main

func main() {
   num := func1()
   println(*num)
}

//go:noinline
func func1() *int {
   n1 := func2()
   *n1++

   return n1
}

//go:noinline
func func2() *int {
   n2 := rand.Intn(99)

   return &n2
}
```

```
F:\hello>go build -gcflags="-m" main.go
# command-line-arguments
./main.go:20:2: moved to heap: n2
```

## pprof
[参考](https://zhuanlan.zhihu.com/p/587988990)
```
package main

import (
    "flag"
    "log"
    "os"
    "runtime/pprof"
    "sync"
    "time"
)

var cpuprofile = flag.String("cpuprofile", "", "write cpu profile to file")

func main() {
    flag.Parse()
    if *cpuprofile != "" {
        f, err := os.Create(*cpuprofile)
        if err != nil {
            log.Fatal(err)
        }
        pprof.StartCPUProfile(f)
        defer pprof.StopCPUProfile()
    }

    var wg sync.WaitGroup
    wg.Add(10)
    for i := 0; i < 10; i++ {
        go work(&wg)
    }
    time.Sleep(3 * time.Second)
    wg.Wait()
}

func work(wg *sync.WaitGroup) {

    var counter int
    for i := 0; i < 1000; i++ {
        time.Sleep(time.Millisecond * 100)
        counter++
    }
    wg.Done()
}
```

```
$ go tool pprof cpupprof cpupprof.prof
File: cpupprof
Type: cpu
Time: Dec 6, 2019 at 10:57am (CST)
Duration: 1.70mins, Total samples = 340ms ( 0.33%)
Entering interactive mode (type "help" for commands, "o" for options)
(pprof) top 10
Showing nodes accounting for 340ms, 100% of 340ms total
Showing top 10 nodes out of 34
      flat  flat%   sum%        cum   cum%
     100ms 29.41% 29.41%      100ms 29.41%  runtime.pthread_cond_wait
      60ms 17.65% 47.06%       60ms 17.65%  runtime.nanotime
      60ms 17.65% 64.71%       60ms 17.65%  runtime.pthread_cond_signal
      50ms 14.71% 79.41%       50ms 14.71%  runtime.pthread_cond_timedwait_relative_np
      20ms  5.88% 85.29%       30ms  8.82%  runtime.notetsleep
      20ms  5.88% 91.18%       20ms  5.88%  runtime.usleep
      10ms  2.94% 94.12%       10ms  2.94%  runtime.pthread_mutex_unlock
      10ms  2.94% 97.06%      160ms 47.06%  runtime.semasleep
      10ms  2.94%   100%       10ms  2.94%  time.Sleep
         0     0%   100%       10ms  2.94%  main.work
(pprof) top5 -cum
Showing nodes accounting for 10ms, 2.94% of 340ms total
Showing top 5 nodes out of 34
      flat  flat%   sum%        cum   cum%
      10ms  2.94%  2.94%      160ms 47.06%  runtime.semasleep
         0     0%  2.94%      130ms 38.24%  runtime.mstart
         0     0%  2.94%      120ms 35.29%  runtime.mcall
         0     0%  2.94%      120ms 35.29%  runtime.park_m
         0     0%  2.94%      120ms 35.29%  runtime.schedule
(pprof) web
(pprof)
```