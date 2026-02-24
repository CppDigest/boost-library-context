# #227 - Suggestion: exceptions from destroyed coroutines should not 'escape' [Closed]

> Username: MiSo1289  
> Created at: 2025-06-16 11:39:55 UTC  
> Updated at: 2025-06-21 11:38:47 UTC  
> Closed at: 2025-06-21 11:38:47 UTC  
> Url: https://github.com/boostorg/cobalt/issues/227  

The main issue I have encountered working with this library is cancellation / destructors; something as innocent as the following code:  
  
```c++  
auto gen_from_chan(channel<int>& chan) -> generator<int>  
{    
  while (chan.is_open())  
    co_yield co_await chan.read();  
  
  co_return -1;  
}  
  
auto promise_from_gen(channel<int>& chan) -> promise<void>  
{  
  auto gen = gen_from_chan(chan);  
  while (gen) {  
    auto i = co_await gen;  
      
    // Simulate async work performed on yielded value.  
    auto tim = steady_timer{co_await this_coro::executor};  
    tim.expires_after(seconds{1});  
    co_await tim.async_wait();  
  }  
}  
```  
  
As of boost 1.88.0, this code will lead to `terminate()` being called and a segmentation fault when cancelling `promise_from_gen()` while it is awaiting the timer task, even if the canceled `promise_from_gen()` is then awaited from the parent task, because the generator destructor will cause an exception to escape from `io_context::run()`. Example reproducing this:  
  
```c++  
auto co_main(int argc, char** argv) -> cobalt::main  
{  
  try {  
    auto chan = cobalt::channel<int>{1};  
    co_await chan.write(1);  
    auto p = promise_from_gen(chan);  
    p.cancel();  
    co_await p;  
  } catch (...) {  
    co_return 1;  
  }  
  
  co_return 0;  
}  
```  
  
Output with GCC 14 on Linux:  
```  
terminate called after throwing an instance of 'boost::detail::with_throw_location<boost::system::system_error>'  
  what():  Operation canceled [system:125]  
```  
  
If we remove the write to the channel, the program exits cleanly, but the same behavior would then occur if we remove `co_await p` - eg. because the `co_main` function is again awaiting something else while letting `p` run in the background...  
  
With the current design, it seems very difficult to design a complex application to handle cancellation of arbitrary subroutines cleanly without crashing.  
  
Workarounds I can currently think of:  
  
1. Do not use `cobalt::main`  /  `cobalt::thread`, instead use a custom `io_context` and wrap `run()` with exception handler that checks for `asio::error::operation_aborted` (and arguably all exceptions) - it feels like this should also be the behavior of `cobalt::main` / `cobalt::thread`, because they are otherwise basically unusable for complex applications, and feel like a trap for new users who aren't aware of this behavior (which is not mentioned in the documentation).  
2. Write all generators (and all promises which might be launched in the background and only later awaited - which doesn't have to be known to the writer of the promise) as try/catch which return `nullopt` in case of exceptions - cumbersome.  
3. Write a try/catch around every await when there is an incomplete generator or background promise in the current coroutine frame, from which we cancel and await the generator / promise - extra cumbersome and cannot actually await from exception handlers.  
  
Given all this, it would be nice if exceptions from destroyed coroutines would just be silently swallowed (or passed to some global handler) - if nothing else then at least from the `operation_aborted` error which is commonly the culprit here due to destroyed unawaited coroutine objects on another coroutine's frame.  
  
Alternative solution (which I actually like much more since it leads to actual deterministic structured lifetimes) - destructor of coroutine object should not cancel it but just go straight to `coroutine_handle::destroy()`. We can still get exception-based cancellation by calling `cancel()` explicitly if that is desired. Or throw the aborted exception in the coroutine, but do it synchronously and if the coroutine suspends instead of returns, destroy it and do not resume it again (I don't like this as much as I'm unable to think of all the corner cases).  
  
(sorry for the rant, I actually like this library a lot)

---

## Comment 1

> Username: MiSo1289  
> Created at: 2025-06-16 12:53:40 UTC  
> Updated at: 2025-06-16 12:54:28 UTC  
> Url: https://github.com/boostorg/cobalt/issues/227#issuecomment-2976546992  

This is the `main()` I am currently using; it has its own pain points, eg. I cannot call `io_context`'s destructor because it will most likely call `terminate()` with `bad_executor` error. It feels like there should be a better and simpler way to do this.  
  
```c++  
auto run(int const argc, char** const argv) -> cobalt::promise<int>;  
  
auto main(int const argc, char** const argv) -> int  
{  
  auto ioc = asio::io_context{};  
  cobalt::this_thread::set_executor(ioc.get_executor());  
  auto task = run(argc, argv);  
  
  auto signals = asio::signal_set{ioc, SIGINT, SIGTERM};  
  signals.async_wait([&](auto ec, auto sig) {  
    if (ec) {  
      return;  
    }  
  
    if (sig == SIGINT) {  
      task.cancel(asio::cancellation_type::total);  
    } else if (sig == SIGTERM) {  
      task.cancel(asio::cancellation_type::terminal);  
    }  
  });  
  
  try {  
    while (true) {  
      try {  
        ioc.run();  
      } catch (...) {  
        // Ignore exceptions escaping from IOC handlers - mostly canceled coroutines that we do not care about.  
      }  
  
      if (task.ready()) {  
        std::exit(task.get());  
        // Don't bother with IOC destructor - it will likely terminate with bad executor exception.  
      }  
    }  
  } catch (std::exception const& err) {  
    spdlog::error("Exception in main thread: {}", err.what());  
  } catch (...) {  
    spdlog::error("Unknown exception in main thread");  
  }  
  std::exit(1);  
}  
```

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2025-06-20 14:32:58 UTC  
> Url: https://github.com/boostorg/cobalt/issues/227#issuecomment-2991849277  

I understand the problem, believe me. But I don't think any solution really works. Swallowing leads to silent bugs which are worse and a global handler doesn't really help because it's amazingly hard to track the exception source in asynchronous code.  
  
For background tasks, it's usually better to use `with` or `task_group` so exceptions get caught. But that of course doesn't help with the generator.  
  
Or you can use `as_tuple` or `as_result` to avoid exceptions.  
  
```cpp  
auto gen_from_chan(channel<int>& chan) -> generator<int>  
{    
  while (chan.is_open())  
  {   
    auto [i, err] = co_await cobalt::as_tuple(chan.read());  
    if (err && co_await cobalt::this_coro::cancelled)  
      co_return -1;  
  }  
  
  co_return -1;  
}  
```  
  
I wish C++ would provide something less verbose, but with the current langauge I can't think of anything.

---

## Comment 3

> Username: MiSo1289  
> Created at: 2025-06-21 11:38:47 UTC  
> Url: https://github.com/boostorg/cobalt/issues/227#issuecomment-2993536538  

Thanks for the reply.  
  
It seems my understanding of destructor behavior in promise / generator was incomplete; I initially believed that destroying an in-progress generator also causes an 'escaped' exception, but that was actually due to the channel being destroyed, not the generator. Promises still have to be awaited before they are destroyed, otherwise they will throw and terminate the program... I think I can live with this though.  
  
Still, I think it would be a lot nicer if promise destructor would just call coroutine_handle::destroy and not attempt to throw an exception in the coroutine. Maybe there could be some compile time option to enable this behavior? It feels very non-c++ that the lifetime of the coroutine is not actually tied to the lifetime of its return object.  
  
(am I understanding this correctly now or is there still some nuance I am missing?)
