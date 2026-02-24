# #39 - Assertion fail / stack-buffer-underflow: catch all exception handler gets called on coroutine destruction [Closed]

> Username: StephanDollberg  
> Created at: 2019-02-12 22:53:19 UTC  
> Updated at: 2019-02-13 07:18:30 UTC  
> Closed at: 2019-02-13 07:18:30 UTC  
> Url: https://github.com/boostorg/coroutine/issues/39  

Hello,  
  
I am not sure whether this is a `boost::asio` or `boost::coroutine` bug but I thought I'd start here as it doesn't really seem related to any asio code. Happy to report it to `boost::asio` though!  
  
the following code:  
  
``` cpp  
#include <iostream>  
#include <chrono>  
  
#include <boost/asio/io_context.hpp>  
#include <boost/asio/spawn.hpp>  
#include <boost/asio/steady_timer.hpp>  
  
int main(int argc, char* argv[]) {  
    boost::asio::io_context io_context;  
  
    boost::asio::spawn(io_context, [&] (boost::asio::yield_context yield) {  
        boost::asio::steady_timer timer(io_context);  
          
        while (true) {  
            try {  
                std::cout << "before" << std::endl;  
                timer.expires_after(std::chrono::seconds(1));  
                timer.async_wait(yield);  
                std::cout << "after" << std::endl;  
            }  
            catch (...) {  
                std::cout << "excep handler" << std::endl;  
            }  
        }  
    });  
  
    for (int i = 0; i < 1; ++i) {  
        io_context.run_one();  
    }  
  
    std::cout << "done" << std::endl;  
  
    return 0;  
}  
```  
  
gives the following output:  
  
  
> before  
> done  
> excep handler  
> before  
> bastelbox: /usr/include/boost/coroutine/detail/push_coroutine_impl.hpp:252: void boost::coroutines::detail::push_coroutine_impl<void>::unwind_stack(): Assertion `is_complete()' failed.  
> Aborted (core dumped)  
  
  
It seems like when the coroutine handler gets destroyed (once the `io_context` gets destroyed) execution is resumed in the catch all exception handler. Using a non-catch all handler like only catching `std::exception` results in normal behaviour (only `before` and `done` is printed).  
  
I am testing this with g++ 8 and boost 1.69. I can reproduce it in both `-O0` and `-O2`. Enabling ASAN also results in a stack-buffer-underflow warning: https://gist.github.com/StephanDollberg/a1ab914d806d3aa4406c5cf840dd2e00  
  
Thanks,  
Stephan

---

## Comment 1

> Username: olk  
> Created at: 2019-02-13 07:18:30 UTC  
> Url: https://github.com/boostorg/coroutine/issues/39#issuecomment-463086923  

you must not catch `detail::forced_unwind exception` as you do with `catch(...)` - the exception is used for unwinding the stack - see section `exceptions` at https://www.boost.org/doc/libs/1_69_0/libs/coroutine/doc/html/coroutine/coroutine/asymmetric.html
