---
title: "golang的定时器NewTimer、NewTicker使用"
summary: "golang的定时器NewTimer、NewTicker使用"
date: 2020-10-27T14:15:02+08:00
categories: ["golang"]
---


```go
package main
 
import (
    "fmt"
    "sync"
    "time"
)
 
/**
*ticker只要定义完成，从此刻开始计时，不需要任何其他的操作，每隔固定时间都会触发。
*timer定时器，是到固定时间后会执行一次
*如果timer定时器要每隔间隔的时间执行，实现ticker的效果，使用 func (t *Timer) Reset(d Duration) bool
 */
func main() {
    var wg sync.WaitGroup
    wg.Add(2)
    //NewTimer 创建一个 Timer，它会在最少过去时间段 d 后到期，向其自身的 C 字段发送当时的时间
    timer1 := time.NewTimer(2 * time.Second)
 
    //NewTicker 返回一个新的 Ticker，该 Ticker 包含一个通道字段，并会每隔时间段 d 就向该通道发送当时的时间。它会调  
   //整时间间隔或者丢弃 tick 信息以适应反应慢的接收者。如果d <= 0会触发panic。关闭该 Ticker 可            
   //以释放相关资源。
    ticker1 := time.NewTicker(2 * time.Second)
 
    go func(t *time.Ticker) {
        defer wg.Done()
        for {
            <-t.C
            fmt.Println("get ticker1", time.Now().Format("2006-01-02 15:04:05"))
        }
    }(ticker1)
 
    go func(t *time.Timer) {
        defer wg.Done()
        for {
            <-t.C
            fmt.Println("get timer", time.Now().Format("2006-01-02 15:04:05"))
            //Reset 使 t 重新开始计时，（本方法返回后再）等待时间段 d 过去后到期。如果调用时t     
            //还在等待中会返回真；如果 t已经到期或者被停止了会返回假。
            t.Reset(2 * time.Second)
        }
    }(timer1)
 
    wg.Wait()
}
```

### 额外说明
1. time.NewTicker定时触发执行任务，当下一次执行到来而当前任务还没有执行结束时，会等待当前任务执行完毕后再执行下一次任务。查阅go官网的文档和经过代码验证。
2. time.NewTimer和Reset()函数实现定时触发，Reset()函数可能失败，经测试。

### Refer
https://learnku.com/articles/23578/the-difference-between-go-timer-and-ticker
