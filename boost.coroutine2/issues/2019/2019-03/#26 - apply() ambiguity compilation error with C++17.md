# #26 - apply() ambiguity compilation error with C++17 [Closed]

> Username: dcolascione  
> Created at: 2019-03-04 04:50:01 UTC  
> Updated at: 2019-04-06 16:18:49 UTC  
> Closed at: 2019-04-06 16:18:49 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/26  

Compilation fails with an ambiguity between Boost.Context and stdlib tuple apply. For some reason, it works fine if we use `std::string` as the coroutine data type, but fails with the ambiguity if we use something like `float`. The code also works fine with the compiler set to C++14 or below.  
  
```  
$ cat foo.cpp   
#include <boost/coroutine2/all.hpp>  
  
int  
main()  
{  
    typedef boost::coroutines2::coroutine<void> coro_t;  
    coro_t::push_type writer([&](coro_t::pull_type& in){});  
    return 0;  
}  
  
~  
$ clang++-7 -std=c++17 foo.cpp  -lboost_coroutine -lboost_context  
In file included from foo.cpp:1:  
In file included from /usr/include/boost/coroutine2/all.hpp:10:  
In file included from /usr/include/boost/coroutine2/coroutine.hpp:15:  
In file included from /usr/include/boost/coroutine2/detail/coroutine.hpp:37:  
In file included from /usr/include/boost/coroutine2/detail/pull_control_block_ecv2.hpp:14:  
In file included from /usr/include/boost/context/execution_context.hpp:13:  
In file included from /usr/include/boost/context/execution_context_v2.hpp:382:  
/usr/include/boost/context/execution_context_v2_void.ipp:58:18: error: call to 'apply' is ambiguous  
        Ctx cc = apply(  
                 ^~~~~  
/usr/include/boost/context/execution_context_v2.hpp:70:18: note: in instantiation of member function  
      'boost::context::detail::record_void<boost::context::execution_context<void>,  
      boost::context::basic_fixedsize_stack<boost::context::stack_traits>, (lambda at  
      /usr/include/boost/coroutine2/detail/push_control_block_ecv2.ipp:325:10)>::run' requested here  
        t = rec->run( t);  
                 ^  
/usr/include/boost/context/execution_context_v2_void.ipp:119:56: note: in instantiation of function template specialization      'boost::context::detail::context_entry<boost::context::detail::record_void<boost::context::execution_context<void>,  
      boost::context::basic_fixedsize_stack<boost::context::stack_traits>, (lambda at  
      /usr/include/boost/coroutine2/detail/push_control_block_ecv2.ipp:325:10)> >' requested here  
    const fcontext_t fctx = make_fcontext( sp, size, & context_entry< record_t >);  
                                                       ^  
/usr/include/boost/context/execution_context_v2_void.ipp:199:24: note: in instantiation of function template specialization      'boost::context::detail::context_create_void<boost::context::execution_context<void>,  
      boost::context::basic_fixedsize_stack<boost::context::stack_traits>, (lambda at  
      /usr/include/boost/coroutine2/detail/push_control_block_ecv2.ipp:325:10)>' requested here  
        fctx_( detail::context_create_void< execution_context >(  
                       ^  
/usr/include/boost/coroutine2/detail/push_control_block_ecv2.ipp:324:5: note: in instantiation of function template specialization  
      'boost::context::execution_context<void>::execution_context<boost::context::basic_fixedsize_stack<boost::context::stack_traits>,  
      (lambda at /usr/include/boost/coroutine2/detail/push_control_block_ecv2.ipp:325:10)>' requested here  
    ctx{ std::allocator_arg, palloc, salloc,  
    ^/usr/include/boost/coroutine2/detail/create_control_block.ipp:50:22: note: in instantiation of function template specialization  
      'boost::coroutines2::detail::push_coroutine<void>::control_block::control_block<boost::context::basic_fixedsize_stack<boost::context::stack_traits>,  
      (lambda at foo.cpp:7:30)>' requested here  
    return new ( sp) ControlBlock{ context::preallocated( sp, size, sctx),  
      (lambda at /usr/include/boost/coroutine2/detail/push_control_block_ecv2.ipp:325:10)>' requested here  
    ctx{ std::allocator_arg, palloc, salloc,  
    ^/usr/include/boost/coroutine2/detail/create_control_block.ipp:50:22: note: in instantiation of function template specialization  
      'boost::coroutines2::detail::push_coroutine<void>::control_block::control_block<boost::context::basic_fixedsize_stack<boost::context::stack_traits>,  
      (lambda at foo.cpp:7:30)>' requested here  
    return new ( sp) ControlBlock{ context::preallocated( sp, size, sctx),  
                     ^  
/usr/include/boost/coroutine2/detail/push_coroutine.ipp:158:10: note: in instantiation of function template specialization  
      'boost::coroutines2::detail::create_control_block<boost::coroutines2::detail::push_coroutine<void>::control_block,  
      boost::context::basic_fixedsize_stack<boost::context::stack_traits>, (lambda at foo.cpp:7:30)>' requested here  
    cb_{ create_control_block< control_block >( salloc, std::forward< Fn >( fn) ) } {  
         ^  
/usr/include/boost/coroutine2/detail/push_coroutine.ipp:153:5: note: in instantiation of function template specialization  
      'boost::coroutines2::detail::push_coroutine<void>::push_coroutine<boost::context::basic_fixedsize_stack<boost::context::stack_traits>,  
      (lambda at foo.cpp:7:30)>' requested here  
    push_coroutine{ default_stack(), std::forward< Fn >( fn) } {  
    ^  
foo.cpp:7:23: note: in instantiation of function template specialization  
      'boost::coroutines2::detail::push_coroutine<void>::push_coroutine<(lambda at foo.cpp:7:30), void>' requested here  
    coro_t::push_type writer([&](coro_t::pull_type& in){});  
                      ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/8.0.1/../../../../include/c++/8.0.1/tuple:1684:5: note: candidate function [with _Fn = (lambda  
      at /usr/include/boost/coroutine2/detail/push_control_block_ecv2.ipp:325:10) &, _Tuple =  
      std::tuple<boost::context::execution_context<void> &&>]  
    apply(_Fn&& __f, _Tuple&& __t)  
    ^  
/usr/include/boost/context/detail/apply.hpp:39:1: note: candidate function [with Fn = (lambda at  
      /usr/include/boost/coroutine2/detail/push_control_block_ecv2.ipp:325:10) &, Tpl =  
      std::tuple<boost::context::execution_context<void> &&>]  
apply( Fn && fn, Tpl && tpl)   
^  
1 error generated.  
```

---

## Comment 1

> Username: olk  
> Created at: 2019-03-06 06:48:46 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/26#issuecomment-469990173  

works for me - used your code in libs/coroutine2/example:  
  
> b2 toolset=clang cflags="-std=c++17" -j 8  
  
and  
  
> b2 toolset=gcc cflags="-std=c++17" -j 8  
  
clang -v  
clang version 7.0.1 (tags/RELEASE_701/final)  
  
gcc -v  
gcc-Version 8.2.1 20181127 (GCC)

---

## Comment 2

> Username: BenjaminW3  
> Created at: 2019-03-16 08:50:28 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/26#issuecomment-473512679  

@dcolascione I was able to produce a nearly identical issue using clang-7.0.0 and libstdc++-7.  
@olk Which standard library has your clang build been using? The error stack above as well as mine have been produced with libstdc++ instead of libc++.

---

## Comment 3

> Username: olk  
> Created at: 2019-03-17 14:52:08 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/26#issuecomment-473672970  

@BenjaminW3  libstdc++ / GNU Standard C++ library version 3 (3.3.6-6)

---

## Comment 4

> Username: BenjaminW3  
> Created at: 2019-03-18 19:44:02 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/26#issuecomment-474071805  

Maybe it is related to newer libstdc++ versions? @dcolascione seems to have used the one from g++-8 and I used `libstdc++-7-dev`.

---

## Comment 5

> Username: olk  
> Created at: 2019-03-18 20:56:49 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/26#issuecomment-474097805  

libstdc++-v3 is the newest version - there s no libstdc++ v7 etc. (libstdc++ uses a different version number than the compiler)

---

## Comment 6

> Username: BenjaminW3  
> Created at: 2019-03-19 17:46:01 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/26#issuecomment-474491596  

At least the package I install is called `libstdc++-7-dev` ;-)  
Maybe the [log of my failing build](https://travis-ci.org/ComputationalRadiationPhysics/alpaka/jobs/507117868) can help to find the reason for the error. It should contain everything to reproduce the error but the CI setup is rather complex.

---

## Comment 7

> Username: Kojoley  
> Created at: 2019-04-05 22:11:17 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/26#issuecomment-480438148  

From the error message it seems that you are using an old Boost version (< 1.67) that  
does not include this https://github.com/boostorg/context/commit/66de43b5ff3592c87468a9f0e19a09430df786ca commit.

---

## Comment 8

> Username: BenjaminW3  
> Created at: 2019-04-06 11:37:26 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/26#issuecomment-480497133  

You are right! A newer boost version already fixed my issue.  
Now it would be interesting if it also helps the original author of this ticket.
