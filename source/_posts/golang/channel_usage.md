---
title: "Channel的使用场景"
date: 2019-12-04T21:03:00+08:00
tags:
  - golang
categories:
  - 技术文章
toc: true
---
## channel的使用场景

把channel用在数据流动的地方：
1. 消息传递、消息过滤
2. 信号广播
3. 事件订阅与广播
4. 请求、响应转发
5. 任务分发
6. 结果汇总
7. 并发控制
8. 同步与异步
9. …
<!--more-->

## channel的基本操作和注意事项

##### channel存在3种状态：

1. nil，未初始化的状态，只进行了声明，或者手动赋值为nil
2. active，正常的channel，可读或者可写
3. closed，已关闭，千万不要误认为关闭channel后，channel的值是nil

##### channel可进行3种操作：

1. 读
2. 写
3. 关闭

####  把这3种操作和3种channel状态可以组合出9种情况：

![状态组合](/images/golang/channel_usage.jpg)

对于nil通道的情况，也并非完全遵循上表，有1个特殊场景：当nil的通道在select的某个case中时，这个case会阻塞，但不会造成死锁。

## 使用channel的10种常用操作

#### 使用for range读channel

* 场景：当需要不断从channel读取数据时
* 原理：使用`for ... range`读取channel，这样既安全又便利，当channel关闭时，for循环会自动退出，无需主动监测channel是否关闭，可以防止读取已经关闭的channel，造成读到数据为通道所存储的数据类型的零值。
* 用法：

``` go
for x := range ch{
    fmt.Println(x)
}
```

#### 使用_,ok判断channel是否关闭

* 场景：读channel，但不确定channel是否关闭时
* 原理：读已关闭的channel会造成panic，如果不确定channel，需要使用ok进行检测。ok的结果和含义：
* * `true`：读到数据，并且通道没有关闭。
* * `false`：通道关闭，无数据读到。
* 用法：

```go
if v, ok := <- ch; ok {
    fmt.Println(v)
}
```

#### 使用select处理多个channel

* 场景：需要对多个通道进行同时处理，但只处理最先发生的channel时
* 原理：select可以同时监控多个通道的情况，只处理未阻塞的case。当通道为nil时，对应的case永远为阻塞，无论读写。特殊关注：普通情况下，对nil的通道写操作是要panic的。
* 用法：

```go
// 分配job时，如果收到关闭的通知则退出，不分配job
func (h *Handler) handle(job *Job) {



select {
    case h.jobCh<-job:
        return 
    case <-h.stopCh:
        return
    }
}
```

#####  使用channel的声明控制读写权限

* 场景：协程对某个通道只读或只写时
* 目的：A. 使代码更易读、更易维护，B. 防止只读协程对通道进行写数据，但通道已关闭，造成panic。
* 用法：
* * 如果协程对某个channel只有写操作，则这个channel声明为只写。
* * 如果协程对某个channel只有读操作，则这个channe声明为只读。

```go
// 只有generator进行对outCh进行写操作，返回声明
// <-chan int，可以防止其他协程乱用此通道，造成隐藏bug
func generator(int n) <-chan int {
    outCh := make(chan int)
    go func(){
        for i:=0;i<n;i++{
            outCh<-i
        }
    }()
    return outCh
}

// consumer只读inCh的数据，声明为<-chan int
// 可以防止它向inCh写数据
func consumer(inCh <-chan int) {
    for x := range inCh {
        fmt.Println(x)
    }
}
```

#### 使用缓冲channel增强并发和异步

* 场景：异步和并发
* 原理：A. 有缓冲通道是异步的，无缓冲通道是同步的，B. 有缓冲通道可供多个协程同时处理，在一定程度可提高并发性。
* 用法：

```go
// 无缓冲，同步
ch1 := make(chan int)
ch2 := make(chan int, 0)
// 有缓冲，异步
ch3 := make(chan int, 1)
// 使用5个`do`协程同时处理输入数据
func test() {
    inCh := generator(100)
    outCh := make(chan int, 10)

    for i := 0; i < 5; i++ {
        go do(inCh, outCh)
    }

    for r := range outCh {
        fmt.Println(r)
    }
}

func do(inCh <-chan int, outCh chan<- int) {
    for v := range inCh {
        outCh <- v * v
    }
}
```

#### 为操作加上超时

* 场景：需要超时控制的操作
* 原理：使用`select`和`time.After`，看操作和定时器哪个先返回，处理先完成的，就达到了超时控制的效果
* 用法：

```go
func doWithTimeOut(timeout time.Duration) (int, error) {
    select {
    case ret := <-do():
        return ret, nil
    case <-time.After(timeout):
        return 0, errors.New("timeout")
    }
}

func do() <-chan int {
    outCh := make(chan int)
    go func() {
        // do work
    }()
    return outCh
}
```

#### 使用time实现channel无阻塞读写

* 场景：并不希望在channel的读写上浪费时间
* 原理：是为操作加上超时的扩展，这里的操作是channel的读或写
* 用法：

```go
func unBlockRead(ch chan int) (x int, err error) {
    select {
    case x = <-ch:
        return x, nil
    case <-time.After(time.Microsecond):
        return 0, errors.New("read time out")
    }
}

func unBlockWrite(ch chan int, x int) (err error) {
    select {
    case ch <- x:
        return nil
    case <-time.After(time.Microsecond):
        return errors.New("read time out")
    }
}
```

*注：time.After等待可以替换为default，则是channel阻塞时，立即返回的效果*

#### 使用close(ch)关闭所有下游协程

* 场景：退出时，显示通知所有协程退出
* 原理：所有读ch的协程都会收到close(ch)的信号
* 用法：

```go
func (h *Handler) Stop() {
    close(h.stopCh)

    // 可以使用WaitGroup等待所有协程退出
}

// 收到停止后，不再处理请求
func (h *Handler) loop() error {
    for {
        select {
        case req := <-h.reqCh:
            go handle(req)
        case <-h.stopCh:
            return
        }
    }
}
```

####  使用chan struct{}作为信号channel

* 场景：使用channel传递信号，而不是传递数据时
* 原理：没数据需要传递时，传递空struct
* 用法：

```go
// 上例中的Handler.stopCh就是一个例子，stopCh并不需要传递任何数据
// 只是要给所有协程发送退出的信号
type Handler struct {
    stopCh chan struct{}
    reqCh chan *Request
}
```

#### 使用channel传递结构体的指针而非结构体

* 场景：使用channel传递结构体数据时
* 原理：channel本质上传递的是数据的拷贝，拷贝的数据越小传输效率越高，传递结构体指针，比传递结构体更高效
* 用法：

```go
reqCh chan *Request

// 好过
reqCh chan Request
```

