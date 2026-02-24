# #341 - Possible use after free when constructing executor [Closed]

> Username: ndusart  
> Created at: 2020-04-09 08:52:00 UTC  
> Updated at: 2020-12-30 01:13:58 UTC  
> Closed at: 2020-12-30 01:13:57 UTC  
> Url: https://github.com/boostorg/asio/issues/341  

Hi,  
  
clang-tidy warns me about possible use-after-free when I'm constructing a `boost::asio::tcp::socket`. It seems to happen when it construct an executor.  
  
Here is a small example that reproduce the issue:  
  
```  
#include <boost/asio/io_context.hpp>  
#include <boost/asio/ip/tcp.hpp>  
  
int main() {  
  boost::asio::io_context ioc;  
  boost::asio::ip::tcp::socket socket(ioc);  
}  
```  
  
clang-tidy outputs this warning when issuing `clang-tidy example.cpp` in a shell:  
```  
1 warning generated.  
/usr/include/boost/asio/executor.hpp:324:7: warning: Use of memory after it is freed [clang-analyzer-cplusplus.NewDelete]  
      impl_->destroy();  
      ^  
/home/nicolas/Downloads/asio_leak.cpp:6:32: note: Calling constructor for 'basic_stream_socket<boost::asio::ip::tcp, boost::asio::executor>'  
  boost::asio::ip::tcp::socket socket(ioc);  
                               ^  
/usr/include/boost/asio/basic_stream_socket.hpp:112:7: note: Calling constructor for 'basic_socket<boost::asio::ip::tcp, boost::asio::executor>'  
    : basic_socket<Protocol, Executor>(context)  
      ^  
/usr/include/boost/asio/basic_socket.hpp:132:7: note: Calling constructor for 'io_object_impl<boost::asio::detail::reactive_socket_service<boost::asio::ip::tcp>, boost::asio::executor>'  
    : impl_(context)  
      ^  
/usr/include/boost/asio/detail/io_object_impl.hpp:87:32: note: Calling constructor for 'executor'  
      implementation_executor_(context.get_executor(),  
                               ^  
/usr/include/boost/asio/impl/executor.hpp:333:11: note: Calling 'impl::create'  
  : impl_(impl<Executor, std::allocator<void> >::create(e))  
          ^  
/usr/include/boost/asio/impl/executor.hpp:136:13: note: Calling constructor for 'raw_mem'  
    raw_mem mem(a);  
            ^  
/usr/include/boost/asio/impl/executor.hpp:233:14: note: Calling 'new_allocator::allocate'  
        ptr_(allocator_.allocate(1))  
             ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/9.3.0/../../../../include/c++/9.3.0/ext/new_allocator.h:104:2: note: Taking false branch  
        if (__n > this->max_size())  
        ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/9.3.0/../../../../include/c++/9.3.0/ext/new_allocator.h:114:27: note: Memory is allocated  
        return static_cast<_Tp*>(::operator new(__n * sizeof(_Tp)));  
                                 ^  
/usr/include/boost/asio/impl/executor.hpp:233:14: note: Returned allocated memory  
        ptr_(allocator_.allocate(1))  
             ^  
/usr/include/boost/asio/impl/executor.hpp:136:13: note: Returning from constructor for 'raw_mem'  
    raw_mem mem(a);  
            ^  
/usr/include/boost/asio/impl/executor.hpp:333:11: note: Returned allocated memory  
  : impl_(impl<Executor, std::allocator<void> >::create(e))  
          ^  
/usr/include/boost/asio/detail/io_object_impl.hpp:87:32: note: Returning from constructor for 'executor'  
      implementation_executor_(context.get_executor(),  
                               ^  
/usr/include/boost/asio/detail/io_object_impl.hpp:87:53: note: Calling '~executor'  
      implementation_executor_(context.get_executor(),  
                                                    ^  
/usr/include/boost/asio/executor.hpp:86:5: note: Calling 'executor::destroy'  
    destroy();  
    ^  
/usr/include/boost/asio/executor.hpp:323:9: note: Field 'impl_' is non-null  
    if (impl_)  
        ^  
/usr/include/boost/asio/executor.hpp:323:5: note: Taking true branch  
    if (impl_)  
    ^  
/usr/include/boost/asio/executor.hpp:324:7: note: Calling 'impl::destroy'  
      impl_->destroy();  
      ^  
/usr/include/boost/asio/impl/executor.hpp:158:9: note: Assuming the condition is true  
    if (--ref_count_ == 0)  
        ^  
/usr/include/boost/asio/impl/executor.hpp:158:5: note: Taking true branch  
    if (--ref_count_ == 0)  
    ^  
/usr/include/boost/asio/impl/executor.hpp:163:7: note: Calling 'new_allocator::deallocate'  
      alloc.deallocate(p, 1);  
      ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/9.3.0/../../../../include/c++/9.3.0/ext/new_allocator.h:128:2: note: Memory is released  
        ::operator delete(__p);  
        ^  
/usr/include/boost/asio/impl/executor.hpp:163:7: note: Returning; memory was released via 1st parameter  
      alloc.deallocate(p, 1);  
      ^  
/usr/include/boost/asio/executor.hpp:324:7: note: Returning; memory was released  
      impl_->destroy();  
      ^  
/usr/include/boost/asio/executor.hpp:86:5: note: Returning; memory was released  
    destroy();  
    ^  
/usr/include/boost/asio/detail/io_object_impl.hpp:87:53: note: Returning from '~executor'  
      implementation_executor_(context.get_executor(),  
                                                    ^  
/usr/include/boost/asio/basic_socket.hpp:132:7: note: Returning from constructor for 'io_object_impl<boost::asio::detail::reactive_socket_service<boost::asio::ip::tcp>, boost::asio::executor>'  
    : impl_(context)  
      ^  
/usr/include/boost/asio/basic_stream_socket.hpp:112:7: note: Returning from constructor for 'basic_socket<boost::asio::ip::tcp, boost::asio::executor>'  
    : basic_socket<Protocol, Executor>(context)  
      ^  
/home/nicolas/Downloads/asio_leak.cpp:6:32: note: Returning from constructor for 'basic_stream_socket<boost::asio::ip::tcp, boost::asio::executor>'  
  boost::asio::ip::tcp::socket socket(ioc);  
                               ^  
/home/nicolas/Downloads/asio_leak.cpp:7:1: note: Calling '~basic_stream_socket'  
}  
^  
/usr/include/boost/asio/basic_stream_socket.hpp:331:3: note: Calling '~basic_socket'  
  }  
  ^  
/usr/include/boost/asio/basic_socket.hpp:1797:3: note: Calling '~io_object_impl'  
  }  
  ^  
/usr/include/boost/asio/detail/io_object_impl.hpp:118:3: note: Calling implicit destructor for 'io_object_executor<boost::asio::executor>'  
  }  
  ^  
/usr/include/boost/asio/detail/io_object_impl.hpp:118:3: note: Calling '~executor'  
/usr/include/boost/asio/executor.hpp:86:5: note: Calling 'executor::destroy'  
    destroy();  
    ^  
/usr/include/boost/asio/executor.hpp:323:9: note: Field 'impl_' is non-null  
    if (impl_)  
        ^  
/usr/include/boost/asio/executor.hpp:323:5: note: Taking true branch  
    if (impl_)  
    ^  
/usr/include/boost/asio/executor.hpp:324:7: note: Use of memory after it is freed  
      impl_->destroy();  
      ^  
```  
  
Is it a false positive or am I using asio incorrectly ?

---

## Comment 1

> Username: J-Vernay  
> Created at: 2020-05-09 11:57:14 UTC  
> Url: https://github.com/boostorg/asio/issues/341#issuecomment-626164787  

I have a similar warning with this code:  
```cpp  
#include <asio.hpp>  
#include <asio/steady_timer.hpp>  
#include <cstdio>  
  
void timerHandler(asio::error_code) noexcept { std::puts("End of timer"); }  
  
int main() {  
  asio::io_context ctx;  
  asio::steady_timer timer(ctx, std::chrono::seconds{1});  
  timer.async_wait(&timerHandler);  
  
  std::puts("Before ctx.run()");  
  ctx.run();  
  std::puts("After ctx.run()");  
  return 0;  
}  
```  
  
Doing `clang-tidy --version` gives me:  
```  
LLVM (http://llvm.org/):  
  LLVM version 7.0.1  
    
  Optimized build.  
  Default target: x86_64-pc-linux-gnu  
  Host CPU: skylake  
```  
I am using Asio stand-alone version 1.16.1 .  
  
  
Error from clang-tidy:  
```  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/executor.hpp:316:20: warning: Use of memory after it is freed [clang-analyzer-cplusplus.NewDelete]  
    return impl_ ? impl_->clone() : 0;  
                   ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/mwcpp/main.cpp:11:22: note: Calling constructor for 'basic_waitable_timer<std::chrono::_V2::steady_clock, asio::wait_traits<std::chrono::_V2::steady_clock>, asio::executor>'  
  asio::steady_timer timer(ctx, std::chrono::seconds{1});  
                     ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/basic_waitable_timer.hpp:279:7: note: Calling constructor for 'io_object_impl<asio::detail::deadline_timer_service<asio::detail::chrono_time_traits<std::chrono::_V2::steady_clock, asio::wait_traits<std::chrono::_V2::steady_clock> > >, asio::executor>'  
    : impl_(context)  
      ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/detail/io_object_impl.hpp:86:32: note: Calling constructor for 'executor'  
      implementation_executor_(context.get_executor(),  
                               ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/impl/executor.hpp:332:11: note: Calling 'impl::create'  
  : impl_(impl<Executor, std::allocator<void> >::create(e))  
          ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/impl/executor.hpp:135:13: note: Calling constructor for 'raw_mem'  
    raw_mem mem(a);  
            ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/impl/executor.hpp:232:14: note: Calling 'new_allocator::allocate'  
        ptr_(allocator_.allocate(1))  
             ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/8/../../../../include/c++/8/ext/new_allocator.h:101:2: note: Taking false branch  
        if (__n > this->max_size())  
        ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/8/../../../../include/c++/8/ext/new_allocator.h:105:2: note: Taking false branch  
        if (alignof(_Tp) > __STDCPP_DEFAULT_NEW_ALIGNMENT__)  
        ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/8/../../../../include/c++/8/ext/new_allocator.h:111:27: note: Memory is allocated  
        return static_cast<_Tp*>(::operator new(__n * sizeof(_Tp)));  
                                 ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/impl/executor.hpp:232:14: note: Returned allocated memory  
        ptr_(allocator_.allocate(1))  
             ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/impl/executor.hpp:135:13: note: Returning from constructor for 'raw_mem'  
    raw_mem mem(a);  
            ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/impl/executor.hpp:332:11: note: Returned allocated memory  
  : impl_(impl<Executor, std::allocator<void> >::create(e))  
          ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/detail/io_object_impl.hpp:86:32: note: Returning from constructor for 'executor'  
      implementation_executor_(context.get_executor(),  
                               ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/detail/io_object_impl.hpp:86:53: note: Calling '~executor'  
      implementation_executor_(context.get_executor(),  
                                                    ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/executor.hpp:85:5: note: Calling 'executor::destroy'  
    destroy();  
    ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/executor.hpp:322:5: note: Taking true branch  
    if (impl_)  
    ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/executor.hpp:323:7: note: Calling 'impl::destroy'  
      impl_->destroy();  
      ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/impl/executor.hpp:157:5: note: Taking true branch  
    if (--ref_count_ == 0)  
    ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/impl/executor.hpp:162:7: note: Calling 'new_allocator::deallocate'  
      alloc.deallocate(p, 1);  
      ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/8/../../../../include/c++/8/ext/new_allocator.h:119:2: note: Taking false branch  
        if (alignof(_Tp) > __STDCPP_DEFAULT_NEW_ALIGNMENT__)  
        ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/8/../../../../include/c++/8/ext/new_allocator.h:125:2: note: Memory is released  
        ::operator delete(__p);  
        ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/impl/executor.hpp:162:7: note: Returning; memory was released via 1st parameter  
      alloc.deallocate(p, 1);  
      ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/executor.hpp:323:7: note: Returning; memory was released  
      impl_->destroy();  
      ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/executor.hpp:85:5: note: Returning; memory was released  
    destroy();  
    ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/detail/io_object_impl.hpp:86:53: note: Returning from '~executor'  
      implementation_executor_(context.get_executor(),  
                                                    ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/basic_waitable_timer.hpp:279:7: note: Returning from constructor for 'io_object_impl<asio::detail::deadline_timer_service<asio::detail::chrono_time_traits<std::chrono::_V2::steady_clock, asio::wait_traits<std::chrono::_V2::steady_clock> > >, asio::executor>'  
    : impl_(context)  
      ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/mwcpp/main.cpp:11:22: note: Returning from constructor for 'basic_waitable_timer<std::chrono::_V2::steady_clock, asio::wait_traits<std::chrono::_V2::steady_clock>, asio::executor>'  
  asio::steady_timer timer(ctx, std::chrono::seconds{1});  
                     ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/mwcpp/main.cpp:12:3: note: Calling 'basic_waitable_timer::async_wait'  
  timer.async_wait(&timerHandler);  
  ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/basic_waitable_timer.hpp:711:12: note: Calling 'async_initiate<void (*)(std::error_code) noexcept, void (std::error_code), asio::basic_waitable_timer<std::chrono::_V2::steady_clock, asio::wait_traits<std::chrono::_V2::steady_clock>, asio::executor>::initiate_async_wait, >'  
    return async_initiate<WaitHandler, void (asio::error_code)>(  
           ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/async_result.hpp:363:10: note: Calling 'async_result::initiate'  
  return async_result<typename decay<CompletionToken>::type,  
         ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/async_result.hpp:151:5: note: Calling 'initiate_async_wait::operator()'  
    ASIO_MOVE_CAST(Initiation)(initiation)(  
    ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/detail/config.hpp:145:31: note: expanded from macro 'ASIO_MOVE_CAST'  
# define ASIO_MOVE_CAST(type) static_cast<type&&>  
                              ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/basic_waitable_timer.hpp:744:7: note: Calling 'deadline_timer_service::async_wait'  
      self_->impl_.get_service().async_wait(  
      ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/detail/deadline_timer_service.hpp:237:21: note: Calling constructor for 'wait_handler<void (*)(std::error_code) noexcept, asio::detail::io_object_executor<asio::executor>>'  
    p.p = new (p.v) op(handler, io_ex);  
                    ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/detail/wait_handler.hpp:40:7: note: Calling copy constructor for 'io_object_executor<asio::executor>'  
      io_executor_(ex)  
      ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/detail/io_object_executor.hpp:42:7: note: Calling copy constructor for 'executor'  
    : executor_(other.executor_),  
      ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/executor.hpp:60:13: note: Calling 'executor::clone'  
    : impl_(other.clone())  
            ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/executor.hpp:316:12: note: '?' condition is true  
    return impl_ ? impl_->clone() : 0;  
           ^  
/home/julienv/cpp/proj/my-website-in-cpp/code/3rd-party/include/asio/executor.hpp:316:20: note: Use of memory after it is freed  
    return impl_ ? impl_->clone() : 0;  
                   ^  
```

---

## Comment 2

> Username: Zitrax  
> Created at: 2020-07-02 22:28:43 UTC  
> Updated at: 2020-07-02 22:34:02 UTC  
> Url: https://github.com/boostorg/asio/issues/341#issuecomment-653245592  

Same here on clang/clang-tidy 10.0.1 with standalone asio on 1.13.0.  
  
Any better way to silence this for now other than turning off the whole cplusplus.NewDelete checker?

---

## Comment 3

> Username: ghost  
> Created at: 2020-12-30 01:13:56 UTC  
> Url: https://github.com/boostorg/asio/issues/341#issuecomment-752293585  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#758](https://github.com/chriskohlhoff/asio/issues/758).
