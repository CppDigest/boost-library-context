# #224 - Memory leak when executor is not executed [Closed]

> Username: kone-tlammi  
> Created at: 2025-04-10 05:00:41 UTC  
> Updated at: 2025-06-20 16:40:29 UTC  
> Closed at: 2025-06-20 16:40:29 UTC  
> Url: https://github.com/boostorg/cobalt/issues/224  

# Issue  
I was writing unit tests and hit address sanitizer memory leak issues.  
  
Minimal code to reproduce the issue:  
```C++  
#include <boost/asio.hpp>  
#include <boost/cobalt.hpp>  
#include <print>  
  
namespace cobalt = boost::cobalt;  
namespace asio = boost::asio;  
  
cobalt::task<void> tsk() {  
  std::println("Hello, World!");  
  co_return;  
}  
  
int main() {  
  auto ioc = asio::io_context();  
  cobalt::spawn(ioc.get_executor(), tsk(), boost::asio::detached);  
}  
```  
I get the leak detection with commands  
```  
g++ -std=c++23 -fsanitize=address ./main.cpp -lboost_cobalt  
clang++ -std=c++23 -fsanitize=address ./main.cpp -lboost_cobalt  
clang++ -std=c++23 -fsanitize=leak ./main.cpp -lboost_cobalt  
```  
  
`g++`'s leak detector does not report anything.  
  
When modifying the code a bit I can see a growing trend in memory usage using `pmap -x`:  
```C++  
#include <boost/asio.hpp>  
#include <boost/cobalt.hpp>  
#include <print>  
  
namespace cobalt = boost::cobalt;  
namespace asio = boost::asio;  
using namespace std::literals;  
  
cobalt::task<void> tsk() {  
  std::println("Hello, World!");  
  co_return;  
}  
  
int main() {  
  while (true) {  
    auto ioc = asio::io_context();  
    cobalt::spawn(ioc.get_executor(), tsk(), boost::asio::detached);  
    std::this_thread::sleep_for(10ms);  
  }  
}  
```  
  
# System Information:  
- g++ version: 14.2.1  
- clang++ version: 19.1.7  
- boost version: 1.87.0-3  
    - Checked also with boost-cobalt's HEAD  
- OS: Manjaro Linux  
# Asan trace  
```  
Indirect leak of 352 byte(s) in 1 object(s) allocated from:  
    #0 0x7f3a331b697a in operator new(unsigned long, std::align_val_t) /usr/src/debug/gcc/gcc/libsanitizer/asan/asan_new_delete.cpp:107  
    #1 0x5576d91ca2ad in std::pmr::memory_resource::allocate(unsigned long, unsigned long) /usr/include/c++/14.2.1/bits/memory_resource.h:76  
    #2 0x5576d92118cc in std::pmr::polymorphic_allocator<max_align_t>::allocate(unsigned long) /usr/include/c++/14.2.1/bits/memory_resource.h:162  
    #3 0x5576d920eec1 in std::allocator_traits<std::pmr::polymorphic_allocator<max_align_t> >::allocate(std::pmr::polymorphic_allocator<max_align_t>&, unsigned long) /usr/include/c++/14.2.1/bits/memory_resource.h:452  
    #4 0x5576d920d1eb in void* boost::cobalt::allocate_coroutine<std::pmr::polymorphic_allocator<void> >(unsigned long, std::pmr::polymorphic_allocator<void>) /usr/include/boost/cobalt/this_coro.hpp:412  
    #5 0x5576d9209758 in void* boost::cobalt::detail::partial_promise_base<std::pmr::polymorphic_allocator<void> >::operator new<boost::cobalt::detail::async_initiate_spawn::operator()<boost::asio::detail::detached_handler>(boost::asio::detail::detached_handler&&, boost::cobalt::task<void>)::completion_handler>(unsigned long, boost::asio::detail::detached_handler&) /usr/include/boost/cobalt/detail/wrapper.hpp:33  
    #6 0x5576d9205e5b in std::__n4861::coroutine_handle<boost::cobalt::detail::post_coroutine_promise<boost::asio::associated_allocator<boost::cobalt::detail::async_initiate_spawn::operator()<boost::asio::detail::detached_handler>(boost::asio::detail::detached_handler&&, boost::cobalt::task<void>)::completion_handler, std::allocator<void> >::type> > boost::cobalt::detail::post_coroutine<boost::cobalt::detail::async_initiate_spawn::operator()<boost::asio::detail::detached_handler>(boost::asio::detail::detached_handler&&, boost::cobalt::task<void>)::completion_handler>(boost::cobalt::detail::async_initiate_spawn::operator()<boost::asio::detail::detached_handler>(boost::asio::detail::detached_handler&&, boost::cobalt::task<void>)::completion_handler) /usr/include/boost/cobalt/detail/wrapper.hpp:145  
    #7 0x5576d9200448 in void boost::cobalt::detail::async_initiate_spawn::operator()<boost::asio::detail::detached_handler>(boost::asio::detail::detached_handler&&, boost::cobalt::task<void>) /usr/include/boost/cobalt/detail/spawn.hpp:156  
    #8 0x5576d91f9738 in void boost::asio::async_result<boost::asio::detached_t, void (std::__exception_ptr::exception_ptr)>::initiate<boost::cobalt::detail::async_initiate_spawn, boost::asio::detached_t const&, boost::cobalt::task<void> >(boost::cobalt::detail::async_initiate_spawn&&, boost::asio::detached_t const&, boost::cobalt::task<void>&&) /usr/include/boost/asio/impl/detached.hpp:66  
    #9 0x5576d91ed427 in decltype (std::enable_if<std::enable_if<boost::asio::detail::are_completion_signatures<void (std::__exception_ptr::exception_ptr)>::value, boost::asio::detail::async_result_has_initiate_memfn<boost::asio::detached_t const&, void (std::__exception_ptr::exception_ptr)> >::type::value, boost::asio::async_result<std::decay<boost::asio::detached_t const&>::type, void (std::__exception_ptr::exception_ptr)> >::type::initiate(static_cast<boost::cobalt::detail::async_initiate_spawn&&>({parm#1}), static_cast<boost::asio::detached_t const&>({parm#2}), static_cast<boost::cobalt::task<void>&&>({parm#3}))) boost::asio::async_initiate<boost::asio::detached_t const&, void (std::__exception_ptr::exception_ptr), boost::cobalt::detail::async_initiate_spawn, boost::cobalt::task<void> >(boost::cobalt::detail::async_initiate_spawn&&, boost::asio::type_identity<boost::asio::detached_t const&>::type&, boost::cobalt::task<void>&&) /usr/include/boost/asio/async_result.hpp:629  
    #10 0x5576d91e1bca in auto boost::cobalt::spawn<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0ul>, boost::asio::detached_t const&>(boost::asio::io_context::basic_executor_type<std::allocator<void>, 0ul>, boost::cobalt::task<void>&&, boost::asio::detached_t const&) /usr/include/boost/cobalt/spawn.hpp:46  
    #11 0x5576d91c563e in main main.cpp:15  
    #12 0x7f3a32b18487  (/usr/lib/libc.so.6+0x27487) (BuildId: 0b707b217b15b106c25fe51df3724b25848310c0)  
    #13 0x7f3a32b1854b in __libc_start_main (/usr/lib/libc.so.6+0x2754b) (BuildId: 0b707b217b15b106c25fe51df3724b25848310c0)  
    #14 0x5576d91c4a74 in _start (/home/user/code/tmp/memleak+0x14a74) (BuildId: 0df25372cad66ca46b7a5c0ac6822af7420d841a)  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-06-20 14:42:16 UTC  
> Url: https://github.com/boostorg/cobalt/issues/224#issuecomment-2991888792  

Good catch, thank you for reporting.
