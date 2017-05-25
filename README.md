#Libco
Libco is a c/c++ coroutine library that is widely used in WeChat services. It has been running on tens of thousands of machines since 2013.

Author: sunnyxu(sunnyxu@tencent.com), leiffyli(leiffyli@tencent.com), dengoswei@gmail.com(dengoswei@tencent.com), sarlmolchen(sarlmolchen@tencent.com)

By linking with libco, you can easily transform synchronous back-end service into coroutine service. The coroutine service will provide out-standing concurrency compare to multi-thread approach. With the system hook, You can easily coding in synchronous way but asynchronous executed.

You can also use co_create/co_resume/co_yield interfaces to create asynchronous back-end service. These interface will give you more control of coroutines.

By libco copy-stack mode, you can easily build a back-end service support tens of millions of tcp connection.
***
### 简介
libco是微信后台大规模使用的c/c++协程库，2013年至今稳定运行在微信后台的数万台机器上。  

libco通过仅有的几个函数接口 co_create/co_resume/co_yield 再配合 co_poll，可以支持同步或者异步的写法，如线程库一样轻松。同时库里面提供了socket族函数的hook，使得后台逻辑服务几乎不用修改逻辑代码就可以完成异步化改造。

作者: sunnyxu(sunnyxu@tencent.com), leiffyli(leiffyli@tencent.com), dengoswei@gmail.com(dengoswei@tencent.com), sarlmolchen(sarlmolchen@tencent.com)

### libco的特性
- 无需侵入业务逻辑，把多进程、多线程服务改造成协程服务，并发能力得到百倍提升;
- 支持CGI框架，轻松构建web服务(New);
- 支持gethostbyname、mysqlclient、ssl等常用第三库(New);
- 可选的共享栈模式，单机轻松接入千万连接(New);
- 完善简洁的协程编程接口
 * 类pthread接口设计，通过co_create、co_resume等简单清晰接口即可完成协程的创建与恢复；
 * __thread的协程私有变量、协程间通信的协程信号量co_signal (New);
 * 语言级别的lambda实现，结合协程原地编写并执行后台异步任务 (New);
 * 基于epoll/kqueue实现的小而轻的网络框架，基于时间轮盘实现的高性能定时器;
 
 
 
 
 Fast coroutine library.
 
 Hooks system calls   read , write , send, recv , etc with epoll. Keep writing your socket programs as if in blocking mode, and hooking this library , coroutines will run in parallel.
 
 
 Basics:  Coroutines are normal functions , whose stack is allocated on heap. Whenever they need to run , the context associated(contains all register values, stack pointer sp , bp , etc) is copied to the processor and starts execution from there, neat.
  

 
 When you call the  libco's read function ,  it will add your coroutine to listen on epoll(one per thread) and adds to the timeout queue( just a linked list).
 On every eventloop it basically wait on events for all file descriptors and then see all file descriptors which had timeouts ( Max timeout set is 40s = 40000 ms) 
 (In the internal side , there is an array created of 40*1000 millis linked lists, and on every time out takes out the elapsed file descriptors and run the co routines , as there is no data in that time , read reutrns with zero data , which we can assumed timeout or error) 
 

 
Changes from original:
instead of standard EGAIN, EWOULDBLOCK,  this changed version returns LIBCO_POLL_TIMEOUT.
changed co_accept function to set o_nonblock flag correctly.









