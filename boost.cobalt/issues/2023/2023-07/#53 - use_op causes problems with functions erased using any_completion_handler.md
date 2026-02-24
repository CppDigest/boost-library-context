# #53 - use_op causes problems with functions erased using any_completion_handler [Closed]

> Username: anarthal  
> Created at: 2023-07-24 15:05:46 UTC  
> Updated at: 2023-07-25 11:01:48 UTC  
> Closed at: 2023-07-25 11:01:48 UTC  
> Url: https://github.com/boostorg/cobalt/issues/53  

The following code fails to build under clang-16, Linux:  
  
```  
  
#include <boost/asio/any_completion_handler.hpp>  
#include <boost/asio/any_io_executor.hpp>  
#include <boost/asio/async_result.hpp>  
#include <boost/asio/consign.hpp>  
#include <boost/asio/error.hpp>  
#include <boost/asio/steady_timer.hpp>  
#include <boost/asio/this_coro.hpp>  
  
#include <chrono>  
#include <memory>  
  
#include "boost/async/main.hpp"  
#include "boost/async/op.hpp"  
  
namespace async = boost::async;  
  
void async_sleep_impl(  
    boost::asio::any_completion_handler<void(boost::system::error_code)> handler,  
    boost::asio::any_io_executor ex,  
    std::chrono::nanoseconds duration  
)  
{  
    auto timer = std::make_shared<boost::asio::steady_timer>(ex, duration);  
    timer->async_wait(boost::asio::consign(std::move(handler), timer));  
}  
  
template <typename CompletionToken>  
inline auto async_sleep(  
    boost::asio::any_io_executor ex,  
    std::chrono::nanoseconds duration,  
    CompletionToken&& token  
)  
{  
    return boost::asio::async_initiate<CompletionToken, void(boost::system::error_code)>(  
        async_sleep_impl,  
        token,  
        std::move(ex),  
        duration  
    );  
}  
  
async::main co_main(int argc, char** argv)  
{  
    co_await async_sleep(co_await async::this_coro::executor, std::chrono::seconds(1), async::use_op);  
    co_return 0;  
}  
```  
  
With the following error:  
  
```  
[main] Building folder: async   
[build] Starting build  
[proc] Executing command: /usr/bin/cmake --build /home/ruben/workspace/async/__build --config Debug --target all -j 6 --  
[build] [ 41%] Built target boost_async  
[build] Consolidate compiler generated dependencies of target main  
[build] [ 52%] Built target boost_async_example_outcome  
[build] [ 64%] Built target boost_async_example_delay_op  
[build] [ 76%] Built target boost_async_example_echo_server  
[build] [ 88%] Built target boost_async_example_delay  
[build] [ 94%] Building CXX object CMakeFiles/main.dir/main.cpp.o  
[build] In file included from /home/ruben/workspace/async/main.cpp:60:  
[build] /opt/boost-with-async/include/boost/asio/any_completion_handler.hpp:134:12: error: no matching constructor for initialization of 'any_completion_executor'  
[build]     return any_completion_executor(std::nothrow,  
[build]            ^                       ~~~~~~~~~~~~~  
[build] /opt/boost-with-async/include/boost/asio/any_completion_handler.hpp:341:16: note: in instantiation of member function 'boost::asio::detail::any_completion_handler_impl<boost::async::completion_handler<boost::system::error_code>>::immediate_executor' requested here  
[build]         impl)->immediate_executor(candidate);  
[build]                ^  
[build] /opt/boost-with-async/include/boost/asio/any_completion_handler.hpp:448:56: note: in instantiation of function template specialization 'boost::asio::detail::any_completion_handler_immediate_executor_fn::impl<boost::async::completion_handler<boost::system::error_code>>' requested here  
[build]         &any_completion_handler_immediate_executor_fn::impl<Handler>,  
[build]                                                        ^  
[build] /home/ruben/workspace/async/include/boost/async/op.hpp:165:35: note: in instantiation of function template specialization 'boost::asio::any_completion_handler<void (boost::system::error_code)>::any_completion_handler<boost::async::completion_handler<boost::system::error_code>, boost::async::completion_handler<boost::system::error_code>>' requested here  
[build]             std::move(initiation)(std::move(complete),  
[build]                                   ^  
[build] /home/ruben/workspace/async/main.cpp:92:12: note: in instantiation of member function 'boost::asio::async_result<boost::async::use_op_t, void (boost::system::error_code)>::op_impl<void (&)(boost::asio::any_completion_handler<void (boost::system::error_code)>, boost::asio::any_io_executor, std::chrono::duration<long, std::ratio<1, 1000000000>>), boost::asio::any_io_executor, std::chrono::duration<long, std::ratio<1, 1000000000>> &>::initiate' requested here  
[build]     return boost::asio::async_initiate<CompletionToken, void(boost::system::error_code)>(  
[build]            ^  
[build] /opt/boost-with-async/include/boost/asio/impl/any_completion_executor.ipp:45:26: note: candidate constructor not viable: no known conversion from 'decltype(associated_immediate_executor<completion_handler<error_code>, any_io_executor>::get(t, ex))' (aka 'boost::async::detail::completion_handler_noop_executor') to 'const any_completion_executor' for 2nd argument  
[build] any_completion_executor::any_completion_executor(std::nothrow_t,  
[build]                          ^  
[build] /opt/boost-with-async/include/boost/asio/impl/any_completion_executor.ipp:58:26: note: candidate constructor not viable: no known conversion from 'decltype(associated_immediate_executor<completion_handler<error_code>, any_io_executor>::get(t, ex))' (aka 'boost::async::detail::completion_handler_noop_executor') to 'any_completion_executor' for 2nd argument  
[build] any_completion_executor::any_completion_executor(std::nothrow_t,  
[build]                          ^  
[build] /opt/boost-with-async/include/boost/asio/any_completion_executor.hpp:101:3: note: candidate template ignored: substitution failure [with OtherAnyExecutor = nothrow_t]: no type named 'type' in 'boost::asio::constraint<false>'  
[build]   any_completion_executor(OtherAnyExecutor e,  
[build]   ^  
[build] /opt/boost-with-async/include/boost/asio/any_completion_executor.hpp:125:3: note: candidate template ignored: substitution failure [with OtherAnyExecutor = decltype(associated_immediate_executor<completion_handler<error_code>, any_io_executor>::get(t, ex))]: no type named 'type' in 'boost::asio::constraint<false>'  
[build]   any_completion_executor(std::nothrow_t, OtherAnyExecutor e,  
[build]   ^  
[build] /opt/boost-with-async/include/boost/asio/any_completion_executor.hpp:158:3: note: candidate template ignored: substitution failure [with Executor = nothrow_t]: no type named 'type' in 'boost::asio::constraint<false>'  
[build]   any_completion_executor(Executor e,  
[build]   ^  
[build] /opt/boost-with-async/include/boost/asio/any_completion_executor.hpp:180:3: note: candidate template ignored: substitution failure [with Executor = decltype(associated_immediate_executor<completion_handler<error_code>, any_io_executor>::get(t, ex))]: no type named 'type' in 'boost::asio::constraint<false>'  
[build]   any_completion_executor(std::nothrow_t, Executor e,  
[build]   ^  
[build] /opt/boost-with-async/include/boost/asio/impl/any_completion_executor.ipp:34:26: note: candidate constructor not viable: requires 1 argument, but 2 were provided  
[build] any_completion_executor::any_completion_executor(nullptr_t) BOOST_ASIO_NOEXCEPT  
[build]                          ^  
[build] /opt/boost-with-async/include/boost/asio/impl/any_completion_executor.ipp:39:26: note: candidate constructor not viable: requires single argument 'e', but 2 arguments were provided  
[build] any_completion_executor::any_completion_executor(  
[build]                          ^  
[build] /opt/boost-with-async/include/boost/asio/impl/any_completion_executor.ipp:52:26: note: candidate constructor not viable: requires single argument 'e', but 2 arguments were provided  
[build] any_completion_executor::any_completion_executor(  
[build]                          ^  
[build] /opt/boost-with-async/include/boost/asio/impl/any_completion_executor.ipp:29:26: note: candidate constructor not viable: requires 0 arguments, but 2 were provided  
[build] any_completion_executor::any_completion_executor() BOOST_ASIO_NOEXCEPT  
[build]                          ^  
[build] 1 error generated.  
[build] gmake[2]: *** [CMakeFiles/main.dir/build.make:76: CMakeFiles/main.dir/main.cpp.o] Error 1  
[build] gmake[1]: *** [CMakeFiles/Makefile2:161: CMakeFiles/main.dir/all] Error 2  
[build] gmake: *** [Makefile:136: all] Error 2  
[proc] The command: /usr/bin/cmake --build /home/ruben/workspace/async/__build --config Debug --target all -j 6 -- exited with code: 2  
[driver] Build completed: 00:00:02.542  
[build] Build finished with exit code 2  
  
```
