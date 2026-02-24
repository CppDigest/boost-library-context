# #100 - "No child processes" error received with boost::process::async_system [Open]

> Username: andresmeidla  
> Created at: 2019-09-09 07:07:20 UTC  
> Updated at: 2020-12-14 14:13:06 UTC  
> Url: https://github.com/boostorg/process/issues/100  

Receiving exception "No child processes" when running two boost::process::async_system in a row within a coroutine:  
```  
#include <iostream>  
#include <boost/asio/io_service.hpp>  
#include <boost/asio/spawn.hpp>  
#include <boost/process/async_system.hpp>  
  
int main(int argc, const char** argv) {  
    std::cout << "starting" << std::endl;  
    boost::asio::io_service ioService;  
    boost::asio::spawn(ioService, [&ioService](boost::asio::yield_context yc) {  
        try {  
            boost::process::async_system(ioService, yc, "echo hello!1");  
            boost::process::async_system(ioService, yc, "echo hello!2");  
        } catch (const std::exception& e) {  
            std::cout << "error: " << e.what() << std::endl;  
        }  
    });  
    ioService.run();  
    std::cout << "exiting" << std::endl;  
    return 0;  
}  
```  
the output is:  
```  
starting  
hello!1  
hello!2  
error: No child processes  
exiting  
```  
Using boost 1.70.0, running it on Ubuntu 19.04, built it with gcc-8.3.0.  
Is there something im doing wrong here?

---

## Comment 1

> Username: dkl  
> Created at: 2020-12-03 10:04:20 UTC  
> Url: https://github.com/boostorg/process/issues/100#issuecomment-737818968  

Hello,  
  
I've tried to look into this since I saw a similar error. Apparently the error happens because of a problem with `sigchld_service`'s `_receivers` std::vector. `sigchld_service::_handle_signal()` iterates through that vector, and calls completion handlers for any child processes that have exited. But sometimes there is a nested call to `sigchld_service::async_wait()` during this, which may do `_receivers.emplace_back()`, thus reallocating the vector, invalidating the for loop's iterator.  
  
(After that, `sigchld_service::_handle_signal()` is messing with free'd memory, and its mechanism to remove exited child processes from the `_receivers` vector doesn't work anymore. The exited child process remains in the list, causing the "No child processes" error later when `sigchld_service` will try to do `waitpid()` for it again.)  
  
So I'm not sure where exactly the call to `sigchld_service::async_wait()` during `sigchld_service::_handle_signal()` is coming from or whether it is correct for it to appear at that point. It looks something like this: `sigchld_service::_handle_signal()` -> `r.second()` (completion handler stored in `_receivers` list) -> lambda function specified to `sigchld_service::async_wait()` earlier in `io_context_ref::on_success()` -> `func(...)` (from the asyncs/funcs list there) -> `io_context_ref::on_success()` -> `sigchld_service::async_wait()` -> `_receivers.emplace_back()`. If that is a correct call chain though, then a fix might be to rewrite `_handle_signal()` to copy the `_receivers` list temporarily while iterating through it.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2020-12-03 10:12:15 UTC  
> Url: https://github.com/boostorg/process/issues/100#issuecomment-737827959  

I have a fix for this in develop, but sadly didn't get it merged in time for bloody 1.75.0, but hopefully 1.76.0

---

## Comment 3

> Username: dkl  
> Created at: 2020-12-03 10:52:33 UTC  
> Url: https://github.com/boostorg/process/issues/100#issuecomment-737864832  

OK, good to know. I would have suggested a fix for sigchld_service, but that's not needed anymore, seeing that you've already resolved it (even removed the sigchld_service class completely).

---

## Comment 4

> Username: dkl  
> Created at: 2020-12-14 14:13:06 UTC  
> Url: https://github.com/boostorg/process/issues/100#issuecomment-744467072  

I've run some tests with the [signal_set](https://github.com/klemens-morgenstern/boost-process/compare/signal_set) branch, but there are some random test failures - sometimes the SIGCHLD handler is registered too late (after SIGCHLD arrives) and thus never triggers.
