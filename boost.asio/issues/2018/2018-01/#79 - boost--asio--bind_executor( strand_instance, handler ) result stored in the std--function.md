# #79 - boost::asio::bind_executor( strand_instance, handler ) result stored in the std::function [Closed]

> Username: igsol  
> Created at: 2018-01-30 18:39:03 UTC  
> Updated at: 2020-12-30 00:48:05 UTC  
> Closed at: 2020-12-30 00:47:43 UTC  
> Url: https://github.com/boostorg/asio/issues/79  

Hi colleagues,  
  
Very often it could be useful to store wrapped by boost::asio:io_server::strand function object in the std::function instance.  
  
In the last boost 1.66 version there are a lot on methods became deprecated.  
  
Asio documentation suggests to use wrap_executor(strand_instance, func_obj) instead of strand_instance.wrap( func_obj ).  
But the returned result of suggested function behaves differently and breaks my multithreading code.  
  
Please, see bellow provided usage example and pay attention on third failed case.  
The strand is just bypassed. It's easy to see in the call stack from the wrapped handler.   
Hence no expected serialization is performed in multithreading code.  
  
The questions are:  
1. Is observed new behavior a bug or feature?  
2. If it is a feature, what is a recommended way to store result of wrap_executor(...) in the std::function instance keeping strand doing the serialization job?  
  
```c++  
#include <boost/test/unit_test.hpp>  
  
#include <boost/asio.hpp>  
  
#include <thread>  
#include <atomic>  
#include <functional>  
#include <condition_variable>  
  
namespace ba = boost::asio;  
  
enum kind { new_lambda_in_wrapper, deprecated_wrapper_in_function, new_wrapper_in_function };  
//----------------------------------------------------------------------------------------------------------------------  
static void strand_usage( kind k, std::atomic_int & in_strand_cnt, std::atomic_int & call_cnt )  
{  
    auto const num_msg = 42;  
  
    ba::io_context ioc;  
    auto work = ba::make_work_guard( ioc );  
    ba::io_context::strand  strand( ioc );  
    std::condition_variable cond_var;  
    std::mutex              mtx;  
  
    auto lambda = [&]()  
    {  
        in_strand_cnt += strand.running_in_this_thread();  
        ++call_cnt == num_msg ? cond_var.notify_one(),0 : 0;  
    };  
    using vfunc = std::function<void()>;  
  
    for ( auto i = num_msg; i--; )  
    {  
        switch ( k )  
        {  
            case new_lambda_in_wrapper:  
                ba::post( ioc, ba::bind_executor( strand, lambda ) ); // Works!  
            break;  
            case deprecated_wrapper_in_function:  
            {  
                vfunc event_handler{ strand.wrap( lambda ) }; // Works fine! But deprecated.  
                ba::post( ioc, event_handler );  
            }  
            break;  
            case new_wrapper_in_function:  
            {  
                vfunc event_handler{ ba::bind_executor( strand, lambda ) }; // Fail!  
                // Q: How we can store a wrapped by strand handler in the std::function<> ?  
                // A: ???  
                ba::post( ioc, event_handler );  
            }  
            break;  
        }  
    }  
  
    std::thread worker_thread( [&ioc]() { ioc.run(); } );  
  
    std::unique_lock<std::mutex> l( mtx );  
    cond_var.wait( l, [&](){ return call_cnt == num_msg; } );  
    work.reset();  
    worker_thread.join();  
}  
//----------------------------------------------------------------------------------------------------------------------  
BOOST_AUTO_TEST_SUITE( strand )  
//----------------------------------------------------------------------------------------------------------------------  
BOOST_AUTO_TEST_CASE( test_deprecated_wrapper_in_function )  
{  
    std::atomic_int in_strand_cnt{ 0 }; // counter of execution in strand context  
    std::atomic_int call_cnt{ 0 };  // counter of total handler execution  
  
    strand_usage( deprecated_wrapper_in_function, in_strand_cnt, call_cnt );  
    BOOST_CHECK_EQUAL( call_cnt, in_strand_cnt );  
}  
//----------------------------------------------------------------------------------------------------------------------  
BOOST_AUTO_TEST_CASE( test_new_lambda_in_wrapper )  
{  
    std::atomic_int in_strand_cnt{ 0 };  
    std::atomic_int call_cnt{ 0 };  
  
    strand_usage( new_lambda_in_wrapper, in_strand_cnt, call_cnt );  
    BOOST_CHECK_EQUAL( call_cnt, in_strand_cnt );  
}  
//----------------------------------------------------------------------------------------------------------------------  
BOOST_AUTO_TEST_CASE( test_new_wrapper_in_function )  
{  
    std::atomic_int in_strand_cnt{ 0 };  
    std::atomic_int call_cnt{ 0 };  
  
    strand_usage( new_wrapper_in_function, in_strand_cnt, call_cnt );  
    // This test fails with boost 1.66!  
    BOOST_CHECK_EQUAL( call_cnt, in_strand_cnt );  
}  
//----------------------------------------------------------------------------------------------------------------------  
BOOST_AUTO_TEST_SUITE_END()  
```  
  
Thanks beforehand for help.

---

## Comment 1

> Username: arun11299  
> Created at: 2018-02-08 09:02:09 UTC  
> Url: https://github.com/boostorg/asio/issues/79#issuecomment-364045836  

The behaviour you are seeing is expected. Let me try to explain it in steps:  
  
- Your `event_handler ` (std::function<...>) does not have an associated executor. So, the default executor is selected, which is the `system_executor`. For more details check `work_dispatcher` class.  
  
- Now the executor being the `system_executor`, the work (`event_handler`) is dispatched to the executor which just calls the `event_handler` object.  
  
- A call to `event_handler` is nothing but executing `executor_binder` functor which is the return value of `bind_executor`.  
  
- The `executor_binder` is not responsible for running your wrapped task in the correct executor, it will just call the wrapped function in the context of the executor which called it i.e. the `system_executor`.  
  
- If you ask, why #2 test (the now deprecated) works is because of what `strand::wrap` returns. Instead of just executing the wrapped function inside the context of the executor, it will again post it to the executor wrapped by the strand.  
  
Hope this is clear.

---

## Comment 2

> Username: igsol  
> Created at: 2018-02-12 14:31:29 UTC  
> Url: https://github.com/boostorg/asio/issues/79#issuecomment-364939137  

Thanks for answer @arun11299 .  
I am not sure where is a better place to continue the discussion, so I put my comment in the https://github.com/chriskohlhoff/asio/issues/278.

---

## Comment 3

> Username: ghost  
> Created at: 2020-12-30 00:47:23 UTC  
> Url: https://github.com/boostorg/asio/issues/79#issuecomment-752288718  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#595](https://github.com/chriskohlhoff/asio/issues/595).
