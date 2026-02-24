# #374 - boost 1.75 boost::asio::spawn complie error [Closed]

> Username: easyeagel  
> Created at: 2021-01-27 07:18:55 UTC  
> Updated at: 2021-05-19 01:40:46 UTC  
> Closed at: 2021-05-19 01:40:45 UTC  
> Url: https://github.com/boostorg/asio/issues/374  

#include<boost/range/begin.hpp>  
#include<boost/range/end.hpp>  
#include<boost/asio/spawn.hpp>  
  
int main()  
{  
    boost::asio::io_context ioc;  
    boost::asio::io_context::strand strand(ioc);  
    boost::asio::spawn(strand, [](boost::asio::yield_context yield)->void  
        {  
  
        }  
    );  
}  
  
this simple code can complied with 1.72, but not with 1.75.

---

## Comment 1

> Username: hajokirchhoff  
> Created at: 2021-01-28 16:22:08 UTC  
> Url: https://github.com/boostorg/asio/issues/374#issuecomment-769201170  

Same here. My code compiles fine with 1.73, gives a complicated template error message with 1.75.  
  
There is a simple Workaround:  
  
 `spawn(strand.context(), [](...)`  
  
will compile fine (although I haven't yet tested if the code works as intended).  
  
IOW: The conversion from strand to its executor context is broken.  
  
  
Summary:   
`spawn` uses `bind_executor` to pass the strand. This used to take the strand's executor by selecting the correct version of `bind_executor`, but this no longer works, perhaps to changes in enable_ifs. Instead of an executor the strand itself is being used to construct the `executor_` member of the `any_executor`, which fails.  
  
Detailed analysis:  
The error message  (Visual Studio 2017) is (shortened for readability)  
  
> error C2664: 'boost::asio::execution::any_executor< ... >::any_executor(boost::asio::execution::any_executor< ... > &&) noexcept': cannot convert argument 1 from 'E' to 'std::nullptr_t'  
1>        with  
1>        [  
1>            E=boost::asio::io_context::strand  
1>        ]  
  
What happens (IMHO) is the following:  
`spawn` wraps the first parameter in a couple of functions and classes that ultimately call bind_executor.hpp:180  
```  
executor_binder_base(BOOST_ASIO_MOVE_ARG(e) e, BOOST_ASIO_MOVE_ARG(U)( u):  
    executor_(BOOST_ASIO_MOVE_CAST(E)(e)), ...  
```  
  
IOW: The strand parameter gets passed to executor_binder_base, where it is supposed to be moved into `executor_`, which is an executor. There is no executor constructor that takes a strand as a parameter, thus the error message "cannot convert argument 1 from 'E' to std::nullptr_t). The only constructor that takes exactly one parameter is the executor(std::nullptr_t). (see any_executor.hpp:1389)  
  
But the strand shouldn't be passed to the executor_binder_base in the first place.  
  
My guess is that the conditional enable_ifs for the template method "bind_executor" in bind_executor.hpp no longer work as expected or that support for BOOST_ASIO_NO_TS_EXECUTORS causes the issue.  
  
spawn.hpp:504 `spawn(strand, ...`) calls `spawn(bind_executor(strand, &default_spawn_handler), ...`. The strand should be bound with `bind_executor`. Note that this template method exists only if BOOST_ASIO_NO_TS_EXECUTORS has not been defined. Otherwise there is only the `bind_executor` at line 488.  
  
There are two variants of `bind_executor(...)`. The second variant (bind_executor.hpp:510) calls `ctx.get_executor()` and passes the result up the template constructor call chain (whatever).  
  
With the workound above, using strand.context() instead of just strand, `is_convertible<Execution_Context, execution_context>` seems to evaluate to true and so this version of bind_executor is used and in effect `strand.context().get_executor()` is passed up the chain and compiles.  
  
Without the workaround this version of bind_executor is not used. Instead, strand gets passed to the constructor `executor_binder` in bind_executor.hpp:496/497  and causes the compiler error.

---

## Comment 2

> Username: ghost  
> Created at: 2021-05-19 01:40:44 UTC  
> Url: https://github.com/boostorg/asio/issues/374#issuecomment-843680260  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#825](https://github.com/chriskohlhoff/asio/issues/825).
