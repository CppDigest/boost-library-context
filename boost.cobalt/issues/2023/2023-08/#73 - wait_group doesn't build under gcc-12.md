# #73 - wait_group doesn't build under gcc-12 [Closed]

> Username: anarthal  
> Created at: 2023-08-14 15:26:25 UTC  
> Updated at: 2023-09-04 04:46:30 UTC  
> Closed at: 2023-09-04 04:46:30 UTC  
> Url: https://github.com/boostorg/cobalt/issues/73  

The following code:  
  
```  
#include <boost/async/promise.hpp>  
#include <boost/async/wait_group.hpp>  
  
static boost::async::promise<void> f()  
{  
    boost::async::wait_group gp;  
    co_return;  
}  
```  
  
Doesn't build under GCC12, Linux, Debug. It fails with the following error:  
  
```  
[build] /opt/boost-1.83-dev-with-redis/include/boost/async/detail/select.hpp: In instantiation of ‘static boost::async::detail::fork boost::async::detail::select_ranged_impl<Ct, URBG, Range>::awaitable::await_impl(boost::async::detail::select_ranged_impl<Ct, URBG, Range>::awaitable&, std::size_t) [with boost::asio::cancellation_type Ct = boost::asio::cancellation_type::all; URBG = std::mersenne_twister_engine<long unsigned int, 32, 624, 397, 31, 2567483615, 11, 4294967295, 7, 2636928640, 15, 4022730752, 18, 1812433253>&; Range = std::__cxx11::list<boost::async::promise<void> >&; std::size_t = long unsigned int]’:  
[build] /opt/boost-1.83-dev-with-redis/include/boost/async/detail/select.hpp:464:31:   required from ‘bool boost::async::detail::select_ranged_impl<Ct, URBG, Range>::awaitable::await_ready() [with boost::asio::cancellation_type Ct = boost::asio::cancellation_type::all; URBG = std::mersenne_twister_engine<long unsigned int, 32, 624, 397, 31, 2567483615, 11, 4294967295, 7, 2636928640, 15, 4022730752, 18, 1812433253>&; Range = std::__cxx11::list<boost::async::promise<void> >&]’  
[build] /opt/boost-1.83-dev-with-redis/include/boost/async/detail/wait_group.hpp:38:34:   required from here  
[build] /opt/boost-1.83-dev-with-redis/include/boost/async/detail/select.hpp:385:25: error: ‘operator new’ is provided by ‘std::__n4861::__coroutine_traits_impl<boost::async::detail::fork, void>::promise_type’ {aka ‘boost::async::detail::fork::promise_type’} but is not usable with the function signature ‘static boost::async::detail::fork boost::async::detail::select_ranged_impl<Ct, URBG, Range>::awaitable::await_impl(boost::async::detail::select_ranged_impl<Ct, URBG, Range>::awaitable&, std::size_t) [with boost::asio::cancellation_type Ct = boost::asio::cancellation_type::all; URBG = std::mersenne_twister_engine<long unsigned int, 32, 624, 397, 31, 2567483615, 11, 4294967295, 7, 2636928640, 15, 4022730752, 18, 1812433253>&; Range = std::__cxx11::list<boost::async::promise<void> >&; std::size_t = long unsigned int]’  
[build]   385 |     static detail::fork await_impl(awaitable & this_, std::size_t idx)  
[build]       |                         ^~~~~~~~~~  
[build] In file included from /opt/boost-1.83-dev-with-redis/include/boost/async/gather.hpp:12,  
[build]                  from /opt/boost-1.83-dev-with-redis/include/boost/async/detail/wait_group.hpp:13,  
[build]                  from /opt/boost-1.83-dev-with-redis/include/boost/async/wait_group.hpp:8,  
[build]                  from /home/ruben/workspace/servertech-chat/server/src/listener.cpp:9:  
[build] /opt/boost-1.83-dev-with-redis/include/boost/async/detail/gather.hpp: In instantiation of ‘static boost::async::detail::fork boost::async::detail::gather_ranged_impl<Range>::awaitable::await_impl(boost::async::detail::gather_ranged_impl<Range>::awaitable&, std::size_t) [with Range = std::__cxx11::list<boost::async::promise<void> >&; std::size_t = long unsigned int]’:  
[build] /opt/boost-1.83-dev-with-redis/include/boost/async/detail/gather.hpp:322:33:   required from ‘bool boost::async::detail::gather_ranged_impl<Range>::awaitable::await_ready() [with Range = std::__cxx11::list<boost::async::promise<void> >&]’  
[build] /opt/boost-1.83-dev-with-redis/include/boost/async/detail/wait_group.hpp:90:34:   required from here  
[build] /opt/boost-1.83-dev-with-redis/include/boost/async/detail/gather.hpp:281:25: error: ‘operator new’ is provided by ‘std::__n4861::__coroutine_traits_impl<boost::async::detail::fork, void>::promise_type’ {aka ‘boost::async::detail::fork::promise_type’} but is not usable with the function signature ‘static boost::async::detail::fork boost::async::detail::gather_ranged_impl<Range>::awaitable::await_impl(boost::async::detail::gather_ranged_impl<Range>::awaitable&, std::size_t) [with Range = std::__cxx11::list<boost::async::promise<void> >&; std::size_t = long unsigned int]’  
[build]   281 |     static detail::fork await_impl(awaitable & this_, std::size_t idx)  
```  
  
It does build under clang-17.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-08-16 02:10:32 UTC  
> Url: https://github.com/boostorg/cobalt/issues/73#issuecomment-1679862339  

It does build under gcc-13 as well.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-08-16 02:15:11 UTC  
> Url: https://github.com/boostorg/cobalt/issues/73#issuecomment-1679865018  

@anarthal does this work? https://github.com/klemens-morgenstern/async/tree/gcc-12_workaround

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-09-04 04:46:30 UTC  
> Url: https://github.com/boostorg/cobalt/issues/73#issuecomment-1704603616  

Done.
