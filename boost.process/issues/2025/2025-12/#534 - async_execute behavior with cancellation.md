# #534 - async_execute behavior with cancellation [Open]

> Username: jens-diewald  
> Created at: 2025-12-18 15:09:20 UTC  
> Updated at: 2025-12-19 10:12:52 UTC  
> Url: https://github.com/boostorg/process/issues/534  

Hi, I am trying to migrate from v1 to v2 (Boost 1.90).  
  
I want to launch a child process, and terminate it, if it takes too long.  
With v1, I used `process::wait_for` (currently only targeting windows, so that was fine).  
The timeout example in the documentation for v2 hints at using something like `async_execute(process(...)(asio::cancel_after(...))(some_handler)`.  
That cancels the process fine, but I am having trouble to determine in the handler whether the process was terminated or exited okay.  
Experimenting a bit, I found, that using `proc.async_wait(boost::asio::cancel_after(...))(some_handler)` behaves differently.  
  
I am uncertain if that is a bug, or if I am missing something.  
The behavior is as follows:  
```c++  
auto ctx  = boost::asio::io_context{};  
auto proc = boost::process::process(ctx, executable_path);  
auto handle_exit_code = [](boost::system::error_code ec, int exitcode) {/*something*/};  
// variant (a)  
proc.async_wait(boost::asio::cancel_after(std::chrono::nanoseconds(1)))(handle_exit_code);  
// variant (b)  
boost::process::async_execute(std::move(proc))(boost::asio::cancel_after(std::chrono::nanoseconds(1)))(handle_exit_code);  
ctx.run();  
```  
If the process times out in `handle_exit_code`, I get  
* with variant (a): `ec == boost::asio::error::operation_aborted` and `exitcode==259`, which corresponds to `boost::process::v2::detail::still_active`, I guess..? This behavior is useful to me.  
* with variant (b): `ec` is not set (`value==0`) and `exitcode==260`. I have no idea what the `260` is, and it seems very unreliable to depend on it.  
  
Sidenote: The comment at `process::async_wait` says  
```   
/// Asynchronously wait for the process to exit and deliver the native exit-code in the completion handler.  
```  
but the signature of the completion token is `void(error_code, int)`, i.e. it uses `int` and not `native_exit_code_type`. So the comment is wrong and it is not the native exit-code, right?  
There is no such comment at `async_execute`, so maybe it is supposed to behave differently? Either way, it would be nice if the behavior would be documented.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-12-18 15:14:23 UTC  
> Url: https://github.com/boostorg/process/issues/534#issuecomment-3670795866  

I'll need to check the `native_exit_code` type, that might be an oversight.  
  
The async_execute will terminate the process when you cancel (you cancel the execution). If that happens the exit-code is undefined (on windows at least) and you should soley rely on the error-code.  
If you cancel async_wait, you cancel the waiting, so you cannot rely on the exit-code either if the error is set.

---

## Comment 2

> Username: jens-diewald  
> Created at: 2025-12-18 15:35:41 UTC  
> Url: https://github.com/boostorg/process/issues/534#issuecomment-3670882471  

Thanks for the response!  
  
> The async_execute will terminate the process when you cancel (you cancel the execution). If that happens the exit-code is undefined (on windows at least) and you should soley rely on the error-code.  
  
As said above: The error code is never set for me, i.e. `!ec` is always true, even when the execution was canceled.  
Is that intended, or should i get `boost::asio::error::operation_aborted`?  
  
>  If you cancel async_wait, you cancel the waiting, so you cannot rely on the exit-code either if the error is set.  
  
That seems fine. If the child process was terminated by me, I do not care about the exit code. As you say, it is not well defined anyways.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2025-12-18 15:58:35 UTC  
> Url: https://github.com/boostorg/process/issues/534#issuecomment-3670973880  

Ok that sounds like a bug - what program are you running and is `.running()` true before the `async_execute` ?

---

## Comment 4

> Username: jens-diewald  
> Created at: 2025-12-18 20:24:00 UTC  
> Url: https://github.com/boostorg/process/issues/534#issuecomment-3672090949  

>what program are you running   
  
The process I am launching? Some other .exe. I will add a small example with windows command line tool `timeout.exe` below.  
  
>is .running() true before the async_execute ?  
  
Yes.  
  
Here is a the small example:  
```c++  
#include <iostream>  
#include <chrono>  
#include <boost/asio.hpp>  
#include <boost/process.hpp>  
  
int main()  
{  
    auto exe = boost::process::environment::find_executable("timeout.exe");  
    auto handle_exit_code = [&](boost::system::error_code ec, int exitcode)  
    {  
        std::cerr << '\n' << ec << '\n' << exitcode << std::endl;  
    };  
    std::cerr << std::boolalpha;  
    {  
        auto ctx = boost::asio::io_context{};  
        auto proc = boost::process::process(ctx, exe, { "5" }, boost::process::process_stdio{ {}, nullptr, nullptr });  
        std::cerr << "running before proc.async_wait(): " << proc.running() << std::endl;  
        proc.async_wait(boost::asio::cancel_after(std::chrono::seconds(1)))(handle_exit_code);  
        ctx.run();  
    }  
    std::cerr << std::endl;  
    {  
        auto ctx = boost::asio::io_context{};  
        auto proc = boost::process::process(ctx, exe, { "5" }, boost::process::process_stdio{ {}, nullptr, nullptr });  
        std::cerr << "running before async_execute(): " << proc.running() << std::endl;  
        boost::process::async_execute(std::move(proc))(boost::asio::cancel_after(std::chrono::seconds(1)))(handle_exit_code);  
        ctx.run();  
  
    }  
}  
```  
  
Output:  
```  
running before proc.async_wait(): true  
system:995  
259  
  
running before async_execute(): true  
system:0  
260  
```

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2025-12-19 01:26:55 UTC  
> Url: https://github.com/boostorg/process/issues/534#issuecomment-3673035301  

It's possible that 260 is just the exit-code timeout gives you when terminated. I sadly don't know how to check this right now.

---

## Comment 6

> Username: jens-diewald  
> Created at: 2025-12-19 08:13:07 UTC  
> Url: https://github.com/boostorg/process/issues/534#issuecomment-3674033237  

I get 260 with my actual application as well. I just used `timeout.exe` for a self contained example.  
  
But the real issue is, that `ec` is `system:0`, as opposed to `system:995`=`asio::error::operation_aborted`, right?

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2025-12-19 10:12:52 UTC  
> Url: https://github.com/boostorg/process/issues/534#issuecomment-3674434227  

I need to think about this a bit. I reckon my reasoning was this: if you cancel, the execution gets completed. Thus there is no error here, op completed successfully.  
But I get that this is confusing (I had to look this up, too).   
  
If you did this:  
  
```cpp  
  
asio::awaitable<void> run_the_thing(...)  
{  
   int i = co_await async_execute(...);  
  
}  
```  
  
You would expect `async_execute` to throw when run_the_thing gets cancelled and not continue silently.
