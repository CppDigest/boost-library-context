# #19 - thread support [Closed]

> Username: klemens-morgenstern  
> Created at: 2023-07-14 08:51:10 UTC  
> Updated at: 2023-11-17 09:25:40 UTC  
> Closed at: 2023-09-04 04:45:50 UTC  
> Url: https://github.com/boostorg/cobalt/issues/19  

The library is currently strictly single threaded, and the former `async::thread` class has been removed. If we want multi-threading we need would need synchronization primitives, such as mutex etc. (the `sam` stuff, but simpler).  
  
Potentially also thread-safe channels.

---

## Comment 1

> Username: ned14  
> Created at: 2023-07-14 09:34:14 UTC  
> Url: https://github.com/boostorg/cobalt/issues/19#issuecomment-1635584153  

Surely the bare minimum viable support needed here is to reparent something from one executor to another? You can build anything else you want on top of that.

---

## Comment 2

> Username: jsaf0  
> Created at: 2023-07-14 11:26:16 UTC  
> Url: https://github.com/boostorg/cobalt/issues/19#issuecomment-1635723531  

Simple synchronization primitives like `Event` and `Lock` from Python’s asyncio (https://docs.python.org/3/library/asyncio-sync.html) would still be very convenient to have as part of this library - also in a single-threaded context.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-07-14 14:49:11 UTC  
> Url: https://github.com/boostorg/cobalt/issues/19#issuecomment-1635975445  

This was the thread type (that can be restored): https://github.com/klemens-morgenstern/async/commit/a7fe353dfc550e4a4393a1bc24396f648cca6826  
  
```cpp  
boost::async::thread thr()  
{  
  boost::asio::steady_timer tim{co_await boost::asio::this_coro::executor, std::chrono::milliseconds(100)};  
  
  auto exec = co_await boost::asio::this_coro::executor;  
  co_await tim.async_wait(boost::asio::deferred);  
}  
  
boost::async::main co_main(int argc, char ** argv)  
{  
  
   co_await thr();  
   co_return 0;  
}  
```

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2023-07-15 03:08:00 UTC  
> Url: https://github.com/boostorg/cobalt/issues/19#issuecomment-1636638110  

Actually all we need in addition is to be able to safely have `co_await` cross executors:  
  
```cpp  
boost::async::thread thr(async::channel<int> foreign_channel) // < different executor  
{  
  co_await thread_safe(foreign_channel.read()); // has an associated executor  
  co_await thread_safe(foreign_channel.get_executor(), foreign_channel.read());  
}  
```

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2023-07-21 16:59:42 UTC  
> Url: https://github.com/boostorg/cobalt/issues/19#issuecomment-1645995438  

Alternatively we could have a coro that is attached to a thread but can be awaited safely, like this:  
  
```cpp  
  
thread::safe coro(thread & thread_to_run_on)  
{  
  // everything in here runs on `thread_to_run_on`  
   co_await   
}  
```

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2023-08-21 12:26:05 UTC  
> Url: https://github.com/boostorg/cobalt/issues/19#issuecomment-1686233090  

`await_on` doesn't work with select.   
  
`latch` might do:  
  
```cpp  
boost::async::thread thr(latch<int> l)  
{  
  l.notify(42);  
}  
  
boost::async::main co_main(int argc, char ** argv)  
{  
   latch<int> l;  
   thread(l).detach();  
  
   co_return co_await l;  
}  
```  
  
Alternatively, `sam` could be ported. Unlike raw `sam` we can actually detect if we're recursive based onte thread_local and/or coroutine's own executor. Thus you won't accidentally dead-lock.

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2023-08-21 12:26:55 UTC  
> Url: https://github.com/boostorg/cobalt/issues/19#issuecomment-1686234294  

@madmongo1

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2023-09-04 04:45:50 UTC  
> Url: https://github.com/boostorg/cobalt/issues/19#issuecomment-1704603217  

Added an spsc-queue example, we don't need this with unique_handle and the other stuff being in the code.

---

## Comment 9

> Username: pfeatherstone  
> Created at: 2023-11-17 09:25:40 UTC  
> Url: https://github.com/boostorg/cobalt/issues/19#issuecomment-1816021412  

I'm struggling to see why threading has become an issue here, and why we would need synchronization primitives. I'm not an expert like you guys, so that's likely why... However, in Asio, you can use `awaitable` or `coro` in a multi-threaded environment safely, provided you carefully choose your executors, e.g. implicit/explicit strands, or channels. Moreover, users can "bring their own threads" by simply calling `asio::io_context::run` on multiple threads. You don't have to use special threads. I feel like this is causing more confusion to asio users. Maybe cppalliance's book on asio, concurrency and coroutines is much needed. What were @chriskohlhoff 's comments on this library? Thank you everyone for the fantastic work!
