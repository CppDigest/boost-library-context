# #151 - Result of `join` does not model awaitable ? [Closed]

> Username: nyibbang  
> Created at: 2024-01-04 11:16:02 UTC  
> Updated at: 2024-03-29 01:29:46 UTC  
> Closed at: 2024-03-29 01:29:46 UTC  
> Url: https://github.com/boostorg/cobalt/issues/151  

Hello,  
  
I've been trying to use cobalt and tried mixing some joins and races operations, but I've had errors that seem incorrect to me. It seems like the result of `join` does not properly model `awaitable` sometimes.  
  
Here is some code that reproduces the error:  
  
```c++  
#include <boost/cobalt.hpp>  
#include <boost/asio.hpp>  
#include <chrono>  
#include <iostream>  
  
namespace cobalt = boost::cobalt;  
using namespace std::chrono_literals;  
using Timer = cobalt::use_op_t::as_default_on_t<boost::asio::steady_timer>;  
  
cobalt::promise<void> wait_for(std::chrono::milliseconds delay) {  
    Timer timer(co_await cobalt::this_coro::executor, delay);  
    std::cout << "wait_for " << delay << " == started" << std::endl;  
    co_await timer.async_wait();  
    std::cout << "wait_for " << delay << " == done" << std::endl;  
}  
  
cobalt::main co_main(int argc, char** argv) {  
    // This one works:  
    // ===============  
    auto waits = []() -> cobalt::promise<void> {  
        co_await cobalt::join(wait_for(1ms), wait_for(1s));  
    };  
    co_await cobalt::race(  
        wait_for(100ms),  
        waits()  
    );  
  
    // This one does not compile:  
    // ==========================  
    co_await cobalt::race(  
        wait_for(100ms),  
        cobalt::join(wait_for(1ms), wait_for(1s))  
    );  
    co_return 0;  
}  
```  
  
The errors I get are the following:  
  
```  
[build] In file included from /home/my-user/cxx-async/src/main_cobalt.cpp:146:  
[build] In file included from /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt.hpp:19:  
[build] In file included from /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/join.hpp:12:  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/detail/join.hpp:273:9: error: 'auto' in return type deduced as 'in_place_value_t' (aka 'in_place_index_t<0>') here but deduced as 'rt' (aka 'result<void, std::exception_ptr>') in earlier return statement  
[build]   273 |         return system::in_place_value;  
[build]       |         ^  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/result.hpp:96:33: note: in instantiation of member function 'boost::cobalt::detail::join_variadic_impl<boost::cobalt::promise<void>, boost::cobalt::promise<void>>::awaitable::await_resume' requested here  
[build]    96 |     if constexpr (requires {aw_.await_resume(as_result_tag{});})  
[build]       |                                 ^  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/result.hpp:96:29: note: in instantiation of requirement here  
[build]    96 |     if constexpr (requires {aw_.await_resume(as_result_tag{});})  
[build]       |                             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/concepts.hpp:27:9: note: in instantiation of member function 'boost::cobalt::as_result_t<boost::cobalt::detail::join_variadic_impl<boost::cobalt::promise<void>, boost::cobalt::promise<void>>::awaitable &>::await_resume' requested here  
[build]    27 |     {aw.await_resume()};  
[build]       |         ^  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/concepts.hpp:27:6: note: in instantiation of requirement here  
[build]    27 |     {aw.await_resume()};  
[build]       |      ^~~~~~~~~~~~~~~~~  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/concepts.hpp:23:26: note: while substituting template arguments into constraint expression here  
[build]    23 | concept awaitable_type = requires (Awaitable aw, std::coroutine_handle<Promise> h)  
[build]       |                          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
[build]    24 | {  
[build]       | ~  
[build]    25 |     {aw.await_ready()} -> std::convertible_to<bool>;  
[build]       |     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
[build]    26 |     {aw.await_suspend(h)};  
[build]       |     ~~~~~~~~~~~~~~~~~~~~~~  
[build]    27 |     {aw.await_resume()};  
[build]       |     ~~~~~~~~~~~~~~~~~~~~  
[build]    28 | };  
[build]       | ~  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/concepts.hpp:32:9: note: (skipping 4 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
[build]    32 |         awaitable_type<Awaitable, Promise>  
[build]       |         ^  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/detail/race.hpp:217:22: note: while checking constraint satisfaction for template 'await_transform<boost::cobalt::as_result_t<boost::cobalt::detail::join_variadic_impl<boost::cobalt::promise<void>, boost::cobalt::promise<void>>::awaitable &>>' required here  
[build]   217 |           auto res = co_await aw;  
[build]       |                      ^~~~~~~~  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/detail/race.hpp:217:22: note: in instantiation of function template specialization 'boost::cobalt::detail::fork::promise_type::await_transform<boost::cobalt::as_result_t<boost::cobalt::detail::join_variadic_impl<boost::cobalt::promise<void>, boost::cobalt::promise<void>>::awaitable &>>' requested here  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/detail/race.hpp:288:71: note: in instantiation of function template specialization 'boost::cobalt::detail::race_variadic_impl<boost::asio::cancellation_type::all, std::linear_congruential_engine<unsigned long, 48271, 0, 2147483647> &, boost::cobalt::promise<void>, boost::cobalt::detail::join_variadic_impl<boost::cobalt::promise<void>, boost::cobalt::promise<void>>>::awaitable::await_impl<1UL>' requested here  
[build]   288 |           return std::array<detail::fork(*)(awaitable&), tuple_size>{&await_impl<Idx>...};  
[build]       |                                                                       ^  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/detail/race.hpp:374:12: note: in instantiation of function template specialization 'boost::cobalt::detail::race_variadic_impl<boost::asio::cancellation_type::all, std::linear_congruential_engine<unsigned long, 48271, 0, 2147483647> &, boost::cobalt::promise<void>, boost::cobalt::detail::join_variadic_impl<boost::cobalt::promise<void>, boost::cobalt::promise<void>>>::awaitable::awaitable<0UL, 1UL>' requested here  
[build]   374 |     return awaitable{args, g, std::make_index_sequence<tuple_size>{}};  
[build]       |            ^  
[build] /home/my-user/cxx-async/src/main_cobalt.cpp:175:5: note: in instantiation of member function 'boost::cobalt::detail::race_variadic_impl<boost::asio::cancellation_type::all, std::linear_congruential_engine<unsigned long, 48271, 0, 2147483647> &, boost::cobalt::promise<void>, boost::cobalt::detail::join_variadic_impl<boost::cobalt::promise<void>, boost::cobalt::promise<void>>>::operator co_await' requested here  
[build]   175 |     co_await cobalt::race(  
[build]       |     ^  
[build] In file included from /home/my-user/cxx-async/src/main_cobalt.cpp:146:  
[build] In file included from /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt.hpp:12:  
[build] In file included from /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/channel.hpp:284:  
[build] In file included from /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/impl/channel.hpp:12:  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/result.hpp:97:18: error: no matching member function for call to 'await_resume'  
[build]    97 |       return aw_.await_resume(as_result_tag{});  
[build]       |              ~~~~^~~~~~~~~~~~  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/concepts.hpp:27:9: note: in instantiation of member function 'boost::cobalt::as_result_t<boost::cobalt::detail::join_variadic_impl<boost::cobalt::promise<void>, boost::cobalt::promise<void>>::awaitable &>::await_resume' requested here  
[build]    27 |     {aw.await_resume()};  
[build]       |         ^  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/concepts.hpp:27:6: note: in instantiation of requirement here  
[build]    27 |     {aw.await_resume()};  
[build]       |      ^~~~~~~~~~~~~~~~~  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/concepts.hpp:23:26: note: while substituting template arguments into constraint expression here  
[build]    23 | concept awaitable_type = requires (Awaitable aw, std::coroutine_handle<Promise> h)  
[build]       |                          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
[build]    24 | {  
[build]       | ~  
[build]    25 |     {aw.await_ready()} -> std::convertible_to<bool>;  
[build]       |     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
[build]    26 |     {aw.await_suspend(h)};  
[build]       |     ~~~~~~~~~~~~~~~~~~~~~~  
[build]    27 |     {aw.await_resume()};  
[build]       |     ~~~~~~~~~~~~~~~~~~~~  
[build]    28 | };  
[build]       | ~  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/concepts.hpp:32:9: note: while checking the satisfaction of concept 'awaitable_type<boost::cobalt::as_result_t<boost::cobalt::detail::join_variadic_impl<boost::cobalt::promise<void>, boost::cobalt::promise<void>>::awaitable &>, boost::cobalt::detail::fork::promise_type>' requested here  
[build]    32 |         awaitable_type<Awaitable, Promise>  
[build]       |         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/concepts.hpp:32:9: note: while substituting template arguments into constraint expression here  
[build]    32 |         awaitable_type<Awaitable, Promise>  
[build]       |         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/detail/fork.hpp:203:14: note: (skipping 2 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
[build]   203 |     template<awaitable<promise_type> Aw>  
[build]       |              ^  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/detail/race.hpp:217:22: note: while checking constraint satisfaction for template 'await_transform<boost::cobalt::as_result_t<boost::cobalt::detail::join_variadic_impl<boost::cobalt::promise<void>, boost::cobalt::promise<void>>::awaitable &>>' required here  
[build]   217 |           auto res = co_await aw;  
[build]       |                      ^~~~~~~~  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/detail/race.hpp:217:22: note: in instantiation of function template specialization 'boost::cobalt::detail::fork::promise_type::await_transform<boost::cobalt::as_result_t<boost::cobalt::detail::join_variadic_impl<boost::cobalt::promise<void>, boost::cobalt::promise<void>>::awaitable &>>' requested here  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/detail/race.hpp:288:71: note: in instantiation of function template specialization 'boost::cobalt::detail::race_variadic_impl<boost::asio::cancellation_type::all, std::linear_congruential_engine<unsigned long, 48271, 0, 2147483647> &, boost::cobalt::promise<void>, boost::cobalt::detail::join_variadic_impl<boost::cobalt::promise<void>, boost::cobalt::promise<void>>>::awaitable::await_impl<1UL>' requested here  
[build]   288 |           return std::array<detail::fork(*)(awaitable&), tuple_size>{&await_impl<Idx>...};  
[build]       |                                                                       ^  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/detail/race.hpp:374:12: note: in instantiation of function template specialization 'boost::cobalt::detail::race_variadic_impl<boost::asio::cancellation_type::all, std::linear_congruential_engine<unsigned long, 48271, 0, 2147483647> &, boost::cobalt::promise<void>, boost::cobalt::detail::join_variadic_impl<boost::cobalt::promise<void>, boost::cobalt::promise<void>>>::awaitable::awaitable<0UL, 1UL>' requested here  
[build]   374 |     return awaitable{args, g, std::make_index_sequence<tuple_size>{}};  
[build]       |            ^  
[build] /home/my-user/cxx-async/src/main_cobalt.cpp:175:5: note: in instantiation of member function 'boost::cobalt::detail::race_variadic_impl<boost::asio::cancellation_type::all, std::linear_congruential_engine<unsigned long, 48271, 0, 2147483647> &, boost::cobalt::promise<void>, boost::cobalt::detail::join_variadic_impl<boost::cobalt::promise<void>, boost::cobalt::promise<void>>>::operator co_await' requested here  
[build]   175 |     co_await cobalt::race(  
[build]       |     ^  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/detail/join.hpp:237:10: note: candidate function not viable: no known conversion from 'as_result_tag' to 'const as_tuple_tag' for 1st argument  
[build]   237 |     auto await_resume(const as_tuple_tag &)  
[build]       |          ^            ~~~~~~~~~~~~~~~~~~~~  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/detail/join.hpp:223:10: note: candidate function not viable: requires 0 arguments, but 1 was provided  
[build]   223 |     auto await_resume()  
[build]       |          ^  
[build] In file included from /home/my-user/cxx-async/src/main_cobalt.cpp:146:  
[build] In file included from /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt.hpp:25:  
[build] In file included from /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/race.hpp:12:  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/detail/race.hpp:217:16: error: variable has incomplete type 'void'  
[build]   217 |           auto res = co_await aw;  
[build]       |                ^  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/detail/race.hpp:288:71: note: in instantiation of function template specialization 'boost::cobalt::detail::race_variadic_impl<boost::asio::cancellation_type::all, std::linear_congruential_engine<unsigned long, 48271, 0, 2147483647> &, boost::cobalt::promise<void>, boost::cobalt::detail::join_variadic_impl<boost::cobalt::promise<void>, boost::cobalt::promise<void>>>::awaitable::await_impl<1UL>' requested here  
[build]   288 |           return std::array<detail::fork(*)(awaitable&), tuple_size>{&await_impl<Idx>...};  
[build]       |                                                                       ^  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/detail/race.hpp:374:12: note: in instantiation of function template specialization 'boost::cobalt::detail::race_variadic_impl<boost::asio::cancellation_type::all, std::linear_congruential_engine<unsigned long, 48271, 0, 2147483647> &, boost::cobalt::promise<void>, boost::cobalt::detail::join_variadic_impl<boost::cobalt::promise<void>, boost::cobalt::promise<void>>>::awaitable::awaitable<0UL, 1UL>' requested here  
[build]   374 |     return awaitable{args, g, std::make_index_sequence<tuple_size>{}};  
[build]       |            ^  
[build] /home/my-user/cxx-async/src/main_cobalt.cpp:175:5: note: in instantiation of member function 'boost::cobalt::detail::race_variadic_impl<boost::asio::cancellation_type::all, std::linear_congruential_engine<unsigned long, 48271, 0, 2147483647> &, boost::cobalt::promise<void>, boost::cobalt::detail::join_variadic_impl<boost::cobalt::promise<void>, boost::cobalt::promise<void>>>::operator co_await' requested here  
[build]   175 |     co_await cobalt::race(  
[build]       |     ^  
[build] In file included from /home/my-user/cxx-async/src/main_cobalt.cpp:146:  
[build] In file included from /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt.hpp:17:  
[build] In file included from /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/gather.hpp:12:  
[build] In file included from /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/detail/gather.hpp:13:  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/detail/fork.hpp:97:15: error: type 'boost::cobalt::as_result_t<boost::cobalt::detail::join_variadic_impl<boost::cobalt::promise<void>, boost::cobalt::promise<void>>::awaitable &>' does not provide a call operator  
[build]    97 |               (*static_cast<BeginTransaction*>(ptr))();  
[build]       |               ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/detail/fork.hpp:95:14: note: while substituting into a lambda expression here  
[build]    95 |             +[](void * ptr)  
[build]       |              ^  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/detail/race.hpp:217:31: note: in instantiation of function template specialization 'boost::cobalt::detail::fork::set_transaction_function::set_transaction_function<boost::cobalt::as_result_t<boost::cobalt::detail::join_variadic_impl<boost::cobalt::promise<void>, boost::cobalt::promise<void>>::awaitable &>>' requested here  
[build]   217 |           auto res = co_await aw;  
[build]       |                               ^  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/detail/race.hpp:288:71: note: in instantiation of function template specialization 'boost::cobalt::detail::race_variadic_impl<boost::asio::cancellation_type::all, std::linear_congruential_engine<unsigned long, 48271, 0, 2147483647> &, boost::cobalt::promise<void>, boost::cobalt::detail::join_variadic_impl<boost::cobalt::promise<void>, boost::cobalt::promise<void>>>::awaitable::await_impl<1UL>' requested here  
[build]   288 |           return std::array<detail::fork(*)(awaitable&), tuple_size>{&await_impl<Idx>...};  
[build]       |                                                                       ^  
[build] /home/my-user/cxx-async/boost/libs/cobalt/include/boost/cobalt/detail/race.hpp:374:12: note: in instantiation of function template specialization 'boost::cobalt::detail::race_variadic_impl<boost::asio::cancellation_type::all, std::linear_congruential_engine<unsigned long, 48271, 0, 2147483647> &, boost::cobalt::promise<void>, boost::cobalt::detail::join_variadic_impl<boost::cobalt::promise<void>, boost::cobalt::promise<void>>>::awaitable::awaitable<0UL, 1UL>' requested here  
[build]   374 |     return awaitable{args, g, std::make_index_sequence<tuple_size>{}};  
[build]       |            ^  
[build] /home/my-user/cxx-async/src/main_cobalt.cpp:175:5: note: in instantiation of member function 'boost::cobalt::detail::race_variadic_impl<boost::asio::cancellation_type::all, std::linear_congruential_engine<unsigned long, 48271, 0, 2147483647> &, boost::cobalt::promise<void>, boost::cobalt::detail::join_variadic_impl<boost::cobalt::promise<void>, boost::cobalt::promise<void>>>::operator co_await' requested here  
[build]   175 |     co_await cobalt::race(  
[build]       |     ^  
[build] 4 errors generated.  
```  
  
I'm compiling this code with clang 18 with libc++ on an Ubuntu 20.04.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-01-09 13:04:19 UTC  
> Url: https://github.com/boostorg/cobalt/issues/151#issuecomment-1883029495  

That should work, seems like a bug.
