# #25 - Compile errors with recent boost [Closed]

> Username: nikkikom  
> Created at: 2014-10-22 14:31:35 UTC  
> Updated at: 2018-06-26 14:08:27 UTC  
> Closed at: 2015-02-21 16:44:30 UTC  
> Url: https://github.com/boostorg/fiber/issues/25  

There are multiple compile errors with boost 1.57 (beta1) and clang 3.6 in c++11 mode:  
  
clang++11 -I ../include -o simple simple.cpp  
  
In file included from simple.cpp:8:  
In file included from ../include/boost/fiber/all.hpp:11:  
In file included from ../include/boost/fiber/barrier.hpp:16:  
In file included from ../include/boost/fiber/condition.hpp:24:  
../include/boost/fiber/detail/scheduler.hpp:59:14: error: no viable conversion  
      from 'detail::thread_local_ptr<fiber_manager>' to  
      'boost::fibers::fiber_manager _'  
    { return t_; }  
             ^~  
../include/boost/fiber/detail/scheduler.hpp:86:26: note: in instantiation of  
      member function  
      'boost::fibers::detail::thread_local_ptrboost::fibers::fiber_manager::get'  
      requested here  
        if ( ! instance_.get() )  
                         ^  
../include/boost/fiber/detail/scheduler.hpp:71:10: error: no viable overloaded  
      '='  
    { t_ = t; }  
      ~~ ^ ~  
../include/boost/fiber/detail/scheduler.hpp:87:23: note: in instantiation of  
      member function  
      'boost::fibers::detail::thread_local_ptrboost::fibers::fiber_manager::reset'  
      requested here  
            instance_.reset( new fiber_manager() );  
                      ^  
../include/boost/fiber/detail/scheduler.hpp:36:7: note: candidate function (the  
      implicit copy assignment operator) not viable: no known conversion from  
      'boost::fibers::fiber_manager *' to 'const  
      boost::fibers::detail::thread_local_ptrboost::fibers::fiber_manager' for  
      1st argument  
class thread_local_ptr : private noncopyable  
      ^  
In file included from simple.cpp:8:  
In file included from ../include/boost/fiber/all.hpp:11:  
In file included from ../include/boost/fiber/barrier.hpp:16:  
In file included from ../include/boost/fiber/condition.hpp:24:  
In file included from ../include/boost/fiber/detail/scheduler.hpp:20:  
In file included from ../include/boost/fiber/fiber_manager.hpp:20:  
../include/boost/fiber/fiber.hpp:109:32: error: call to 'forward' is ambiguous  
        detail::setup< Fn > s( forward< Fn >( fn), & coro);  
                               ^~~~~~~~~~~~~  
simple.cpp:24:30: note: in instantiation of function template specialization  
      'boost::fibers::fiber::fiber<boost::_bi::bind_t<void, void (_)(const  
      std::__1::basic_string<char> &, int),  
      boost::_bi::list2<boost::_bi::value<const char *>, boost::_bi::value<int>  
      > > >' requested here  
        boost::fibers::fiber f1( boost::bind( fn, "abc", 5) );  
                             ^  
/opt/local/libexec/llvm-3.6/bin/../include/c++/v1/type_traits:1612:1: note:  
      candidate function [with _Tp = boost::_bi::bind_t<void, void (*)(const  
      std::__1::basic_string<char> &, int),  
      boost::_bi::list2<boost::_bi::value<const char *>, boost::_bi::value<int>  
      > >]  
forward(typename std::remove_reference<_Tp>::type& __t) _NOEXCEPT  
^  
/opt/local/include/boost/move/utility_core.hpp:219:21: note: candidate function  
      [with T = boost::_bi::bind_t<void, void (*)(const  
      std::__1::basic_string<char> &, int),  
      boost::_bi::list2<boost::_bi::value<const char *>, boost::_bi::value<int>  
      > >]  
         inline T&& forward(typename ::boost::move_detail::remove_refere...  
                    ^  
In file included from simple.cpp:8:  
In file included from ../include/boost/fiber/all.hpp:11:  
In file included from ../include/boost/fiber/barrier.hpp:16:  
In file included from ../include/boost/fiber/condition.hpp:24:  
In file included from ../include/boost/fiber/detail/scheduler.hpp:20:  
In file included from ../include/boost/fiber/fiber_manager.hpp:20:  
In file included from ../include/boost/fiber/fiber.hpp:25:  
../include/boost/fiber/detail/trampoline.hpp:46:13: error: call to 'forward' is  
      ambiguous  
    Fn fn_( forward< Fn >( from->fn) );  
            ^~~~~~~~~~~~~  
../include/boost/fiber/fiber.hpp:108:50: note: in instantiation of function  
      template specialization  
      'boost::fibers::detail::trampoline<boost::_bi::bind_t<void, void (*)(const  
      std::__1::basic_string<char> &, int),  
      boost::_bi::list2<boost::_bi::value<const char *>, boost::_bi::value<int>  
      > > >' requested here  
        typename coro_t::call_type coro( detail::trampoline< Fn >, attrs...  
                                                 ^  
simple.cpp:24:30: note: in instantiation of function template specialization  
      'boost::fibers::fiber::fiber<boost::_bi::bind_t<void, void (*)(const  
      std::__1::basic_string<char> &, int),  
      boost::_bi::list2<boost::_bi::value<const char *>, boost::_bi::value<int>  
      > > >' requested here  
        boost::fibers::fiber f1( boost::bind( fn, "abc", 5) );  
                             ^  
/opt/local/libexec/llvm-3.6/bin/../include/c++/v1/type_traits:1612:1: note:  
      candidate function [with _Tp = boost::_bi::bind_t<void, void (*)(const  
      std::__1::basic_string<char> &, int),  
      boost::_bi::list2<boost::_bi::value<const char *>, boost::_bi::value<int>  
      > >]  
forward(typename std::remove_reference<_Tp>::type& __t) _NOEXCEPT  
^  
/opt/local/include/boost/move/utility_core.hpp:219:21: note: candidate function  
      [with T = boost::_bi::bind_t<void, void (*)(const  
      std::__1::basic_string<char> &, int),  
      boost::_bi::list2<boost::_bi::value<const char *>, boost::_bi::value<int>  
      > >]  
         inline T&& forward(typename ::boost::move_detail::remove_refere...  
                    ^  
In file included from simple.cpp:8:  
In file included from ../include/boost/fiber/all.hpp:11:  
In file included from ../include/boost/fiber/barrier.hpp:16:  
In file included from ../include/boost/fiber/condition.hpp:24:  
In file included from ../include/boost/fiber/detail/scheduler.hpp:20:  
In file included from ../include/boost/fiber/fiber_manager.hpp:18:  
In file included from ../include/boost/fiber/detail/waiting_queue.hpp:19:  
In file included from ../include/boost/fiber/detail/worker_fiber.hpp:18:  
In file included from /opt/local/include/boost/coroutine/symmetric_coroutine.hpp:12:  
In file included from /opt/local/include/boost/coroutine/detail/symmetric_coroutine_call.hpp:18:  
/opt/local/include/boost/coroutine/detail/symmetric_coroutine_object.hpp:39:25: error:  
      data member instantiated with function type 'void  
      (boost::coroutines::detail::symmetric_coroutine_yield<void *> &)'  
    Fn                  fn_;  
                        ^  
/opt/local/include/boost/coroutine/detail/symmetric_coroutine_call.hpp:122:72: note:  
      in instantiation of template class  
      'boost::coroutines::detail::symmetric_coroutine_object<void *, void  
      (boost::coroutines::detail::symmetric_coroutine_yield<void *> &),  
      boost::coroutines::basic_standard_stack_allocatorboost::coroutines::stack_traits  
      >' requested here  
        internal_stack_ctx.sp = static_cast< char \* >( stack_ctx.sp) - sizeof( o...  
                                                                       ^  
../include/boost/fiber/fiber.hpp:108:36: note: in instantiation of function  
      template specialization  
      'boost::coroutines::detail::symmetric_coroutine_call<void  
      *>::symmetric_coroutine_call<void  
      (boost::coroutines::detail::symmetric_coroutine_yield<void *> &)>'  
      requested here  
        typename coro_t::call_type coro( detail::trampoline< Fn >, attrs...  
                                   ^  
simple.cpp:24:30: note: in instantiation of function template specialization  
      'boost::fibers::fiber::fiber<boost::_bi::bind_t<void, void (*)(const  
      std::__1::basic_string<char> &, int),  
      boost::_bi::list2<boost::_bi::value<const char *>, boost::_bi::value<int>  
      > > >' requested here  
        boost::fibers::fiber f1( boost::bind( fn, "abc", 5) );  
                             ^  
In file included from simple.cpp:8:  
In file included from ../include/boost/fiber/all.hpp:11:  
In file included from ../include/boost/fiber/barrier.hpp:16:  
In file included from ../include/boost/fiber/condition.hpp:24:  
In file included from ../include/boost/fiber/detail/scheduler.hpp:20:  
In file included from ../include/boost/fiber/fiber_manager.hpp:18:  
In file included from ../include/boost/fiber/detail/waiting_queue.hpp:19:  
In file included from ../include/boost/fiber/detail/worker_fiber.hpp:18:  
In file included from /opt/local/include/boost/coroutine/symmetric_coroutine.hpp:12:  
/opt/local/include/boost/coroutine/detail/symmetric_coroutine_call.hpp:127:15: error:  
      assigning to 'impl_type *' (aka 'symmetric_coroutine_impl<void *> *') from  
      incompatible type 'object_t *' (aka 'symmetric_coroutine_object<void *,  
      void (boost::coroutines::detail::symmetric_coroutine_yield<void *> &),  
      stack_allocator> *')  
        impl_ = new ( internal_stack_ctx.sp) object_t(  
              ^ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../include/boost/fiber/fiber.hpp:108:36: note: in instantiation of function  
      template specialization  
      'boost::coroutines::detail::symmetric_coroutine_call<void  
      *>::symmetric_coroutine_call<void  
      (boost::coroutines::detail::symmetric_coroutine_yield<void *> &)>'  
      requested here  
        typename coro_t::call_type coro( detail::trampoline< Fn >, attrs...  
                                   ^  
simple.cpp:24:30: note: in instantiation of function template specialization  
      'boost::fibers::fiber::fiber<boost::_bi::bind_t<void, void (*)(const  
      std::__1::basic_string<char> &, int),  
      boost::_bi::list2<boost::_bi::value<const char *>, boost::_bi::value<int>  
      > > >' requested here  
        boost::fibers::fiber f1( boost::bind( fn, "abc", 5) );  
                             ^  
6 errors generated.

---

## Comment 1

> Username: Gadamatik  
> Created at: 2018-06-25 13:10:19 UTC  
> Url: https://github.com/boostorg/fiber/issues/25#issuecomment-399946057  

Hello @olk,  
  
I'm sorry to come and expose the same prob... Even building fiber on windows... When I do: b2 toolset=gcc cxxflags="-std=c++14" --with-fiber --reconfigure address-model=64 stage...  
it shows me building but doing nothing... And there is no "libboost_fiber-mgw53-mt-d-x64-1_67.a" in stage folder.  
  
I tried with 1.66/1.67, same result...   
  
I'm using GCC 5.3.0 built by MinGW, on windows 7 64bit i7  
  
Thanks for your support.  
Best Regards.

---

## Comment 2

> Username: Gadamatik  
> Created at: 2018-06-26 14:08:27 UTC  
> Url: https://github.com/boostorg/fiber/issues/25#issuecomment-400322251  

Hello,  
  
Maybe my question is stupid or not far to something like that... But I'm just a beginner and one day I will understand how it was so stupid. :)  
  
Thanks and sorry for the noise...
