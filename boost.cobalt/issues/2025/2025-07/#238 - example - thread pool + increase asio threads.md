# #238 - example : thread pool + increase asio threads [Open]

> Username: madkote  
> Created at: 2025-07-21 08:46:49 UTC  
> Updated at: 2025-07-22 11:28:51 UTC  
> Url: https://github.com/boostorg/cobalt/issues/238  

I am looking for minimal example:  
- dispatch cpu work function on a thread pool  
- how to increase asio threads (cobalt is single threaded, but in plain asio additional io threads can be added with `io_threads.emplace_back([&io_main]() { io_main.run(); };`)

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-07-21 13:10:46 UTC  
> Url: https://github.com/boostorg/cobalt/issues/238#issuecomment-3096736889  

1. Are you thinking about a specific thread-pool?  
2. What do you mean? Cobalt won't work if you add more threads.

---

## Comment 2

> Username: madkote  
> Created at: 2025-07-22 09:16:39 UTC  
> Url: https://github.com/boostorg/cobalt/issues/238#issuecomment-3101814751  

thanks for reply @klemens-morgenstern   
  
1. normally there are two options  
  - using explicit thread pool as in examples  
  - use another io context work cpu intesive work (switch it when needed and code stays _async_)   
   
2. I was looking for a way to add more threads to main IO and if that would work with cobalt (I have not tried it yet)  
background: starting `asio::io_context io_main` in more then one threads will add throughput for the handling. Therefore the question, if cobalt relies on ONE context with one thread or is there a possibility to set custom IO context?

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2025-07-22 11:28:51 UTC  
> Url: https://github.com/boostorg/cobalt/issues/238#issuecomment-3102319776  

You can just use `spawn` on a single threaded executor, and you should use `cobalt::this_thread::set_executor` from within.  
  
```cpp  
boost::cobalt::task<void> my_task()  
{  
  boost::cobalt::this_thread::set_executor(co_await boost::cobalt::this_coro::executor);  
  // do your work here  
}  
```  
  
```cpp  
asio::thread_pool tp;  
boost::cobalt::spawn(asio::make_strand(tp), my_task(), asio::detached);  
  
```  
  
You can also use `spawn` accross threads with `use_op`, e.g.:  
  
  
```cpp  
cobalt::main co_main(int, char**)  
{  
   asio::thread_pool tp;  
  // run the task on another thread  
  co_await cobalt::spawn(asio::make_strand(tp), my_task(), cobalt::use_op);  
  co_return 0;  
}  
```  
  
co_main does something very similar, but also connects the signals to cancellations, so that SIGTERM causes a graceful shutdown.
