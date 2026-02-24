# #168 - `cobalt::race` doesn't ignore exception thrown by cancellation [Closed]

> Username: Lyulf  
> Created at: 2024-03-31 15:25:16 UTC  
> Updated at: 2024-04-01 18:07:24 UTC  
> Closed at: 2024-04-01 18:07:23 UTC  
> Url: https://github.com/boostorg/cobalt/issues/168  

When first awaitables processed by `cobalt::race` (or `cobalt::left_race`) is immediately ready then when the next awaitable is cancelled its exception is thrown directly on executor.  
This doesn't happen when awaitables are processed in reverse order (tested using `cobalt::left_race`).  
  
Tested on:  
`Windows 10 64-bit using MSVC 17.9.0 (cl 19.39.33519 for x64)`  
  
Example code:  
```c++  
#include <chrono>  
#include <coroutine>  
#include <iostream>  
  
#include <boost/asio.hpp>  
#include <boost/cobalt.hpp>  
  
namespace asio = boost::asio;  
namespace cobalt = boost::cobalt;  
  
using namespace std::chrono_literals;  
  
namespace  
{  
  
cobalt::promise<void> suspendUntilCancelled()  
{  
  asio::steady_timer timer{co_await cobalt::this_coro::executor};  
  for (;;)  
  {  
    timer.expires_after(1s);  
    co_await timer.async_wait(cobalt::use_op);  
  }  
  
}  
  
cobalt::task<void> test()  
{  
  // Error doesn't happen when std::suspend_never is after suspendUntilCancelled  
  co_await cobalt::left_race(std::suspend_never{}, suspendUntilCancelled());  
}  
  
} // namespace  
  
int main(int /*argc*/, char** /*argv*/)  
{  
  asio::io_context context{1};  
  
  auto future = cobalt::spawn(context.get_executor(), test(), asio::use_future);  
  
  cobalt::this_thread::set_executor(context.get_executor());  
  for (;;)  
  {  
    try  
    {  
      context.run();  
      break;  
    }  
    catch (const std::exception& e)  
    {  
      std::cout << "context: " << e.what() << std::endl;  
    }  
  }  
  
  try  
  {  
    future.get();  
  }  
  catch (const std::exception& e)  
  {  
    std::cout << "future: " << e.what() << std::endl;  
  }  
  
  std::cout << "Finished" << std::endl;  
  
  return 0;  
}  
```  
  
Output:  
```  
context: The I/O operation has been aborted because of either a thread exit or an application request [system:995]  
Finished  
```

---

## Comment 1

> Username: Lyulf  
> Created at: 2024-03-31 16:33:43 UTC  
> Url: https://github.com/boostorg/cobalt/issues/168#issuecomment-2028816564  

After further analysis it seems that the conditions for this problem may be different than what I described.  
  
Error happens 100% of the time for me when I test the above code but it seems to also happen under different conditions. In the code where I initially found the problem both awaitables are initially suspended and the second one resumes first but error still seems to happen.  
  
Unfortunately I am currently unable to provide a second example because the error seems to depend on some external factors that I haven't yet figured out.

---

## Comment 2

> Username: Lyulf  
> Created at: 2024-03-31 16:36:25 UTC  
> Url: https://github.com/boostorg/cobalt/issues/168#issuecomment-2028817389  

When fiddling with debugger I found that `receiver` in `cobalt_promise` is `NULL` when `unhandled_exception` is called.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2024-03-31 22:33:36 UTC  
> Url: https://github.com/boostorg/cobalt/issues/168#issuecomment-2028934231  

This is the intended behaviour, however weird this particular case might be. I haven't really thought of this issue tbh.  
I would be open to changing the specification if we find a better solution.  
  
if you use `left_race` and the left-most argument is ready() (i.e. `await_ready` returns true), it will not consider any argument left of it. The same is true for select with it's random order.  
  
What now happens is that the `left_select` returns without awaiting `suspendUntilCancelled`, which then goes out of scope. When it goes out of scope it detaches and cancels (hence the NULL for receiver). Since it's detached the exception caused by the cancellation must go somewhere, so it'll rethrow it from the main loop. I think in your case I would recommend this:  
  
```cpp  
cobalt::task<void> test()  
{  
  // Error doesn't happen when std::suspend_never is after suspendUntilCancelled  
  auto s = suspendUntilCancelled();  
  co_await cobalt::left_race(std::suspend_never{}, s);  
    
  s.cancel();  
  try {  co_await s; } catch(...) {}  
    
}  
```  
  
Does my explanation make sense?

---

## Comment 4

> Username: Lyulf  
> Created at: 2024-04-01 10:27:29 UTC  
> Url: https://github.com/boostorg/cobalt/issues/168#issuecomment-2029544804  

Thank you for the explanation. I tried the fix in my original code and it worked!  
  
I can see why `cobalt::race` was implemented this way but current it is hard to use `cobalt::promise` with `cobalt::race` since every `cobalt::promise` would need to be cancelled and awaited given random nature of `cobalt::race`. At the same time I can see uses for awaiting results to differentiate thrown errors or if error was thrown at all. Maybe middle ground should be used and only rvalues should be awaited?

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2024-04-01 13:20:22 UTC  
> Url: https://github.com/boostorg/cobalt/issues/168#issuecomment-2029749815  

Well, if you were to pass in a `task` instead and it would not get awaited in the same way and just get destroyed without any side effects. Think of this:  
  
```cpp  
asio::steady_timer tim{co_await cobalt::this_coro::executor, std::chrono::milliseconds(100)};  
co_await cobalt::left_race(do_the_thing, tim);  
```  
  
Here, if `do_the_thing` is ready already, tim is a noop, since operations are lazy. So i never getting touched is ideal.  
I get that it's confusing, but the above snippet seems correct to me.  
  
I appreciate that test-code makes this weird, so please keep me posted on your usage (and send me an email if you don't want to post it on github).

---

## Comment 6

> Username: Lyulf  
> Created at: 2024-04-01 18:07:24 UTC  
> Url: https://github.com/boostorg/cobalt/issues/168#issuecomment-2030269434  

Maybe it is just a part of coroutines that requires getting used to.  
  
There is even this line in documentation that I somehow missed:  
`The race will invoke the functions of the awaitable as if used in a co_await expression or not evaluate them at all`  
  
I think I will close this issue for now and reopen it or create a new one if I think of a good solution for this.
