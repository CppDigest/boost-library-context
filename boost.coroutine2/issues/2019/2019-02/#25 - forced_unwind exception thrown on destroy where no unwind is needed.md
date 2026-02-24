# #25 - forced_unwind exception thrown on destroy where no unwind is needed [Closed]

> Username: alexeiz  
> Created at: 2019-02-27 18:41:12 UTC  
> Updated at: 2020-04-26 04:50:35 UTC  
> Closed at: 2020-04-24 09:23:44 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/25  

It's impossible to avoid exceptions using the coroutines2 library (Boost version 1.69).  
  
Consider this (pretty much the example) code where a coroutine gets created, used and destroyed:  
``` cpp  
#include <boost/coroutine2/all.hpp>  
#include <iostream>  
  
using coro_t = boost::coroutines2::coroutine<int>;  
  
inline auto make_coro()  
{  
    return coro_t::pull_type{  
        [](coro_t::push_type & sink) {  
            sink(1);  
        }  
    };  
}  
  
int main()  
{  
    auto f = make_coro();  
    while(f)  
    {  
        std::cout << f.get() << "\n";  
        f();  
    }  
}  
```  
  
Does the unwind need to happen here on the coroutine destruction?  Doesn't look like it.  However, the `forced_unwind` exception still gets thrown.  Here's the backtrace:  
  
```  
#0  __cxxabiv1::__cxa_throw (... <typeinfo for boost::context::detail::forced_unwind>  
#1  in boost::context::detail::fiber_unwind (t=...)  
    at .../include/boost/context/fiber_fcontext.hpp:58  
#2  in boost::context::fiber::resume()  
    at .../include/boost/context/fiber_fcontext.hpp:289  
#3  in boost::coroutines2::detail::pull_coroutine<int>::control_block::control_block<boost::context::basic_fixedsize_stack<boost::context::stack_traits>, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}>(boost::context::preallocated, boost::context::basic_fixedsize_stack<boost::context::stack_traits>&&, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}&&)::{lambda(boost::context::fiber&&)#1}::operator()(boost::context::fiber) (...)  
    at .../include/boost/coroutine2/detail/pull_control_block_cc.ipp:95  
#4  in std::__invoke_impl<boost::context::fiber, boost::coroutines2::detail::pull_coroutine<int>::control_block::control_block<boost::context::basic_fixedsize_stack<boost::context::stack_traits>, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}>(boost::context::preallocated, boost::context::basic_fixedsize_stack<boost::context::stack_traits>&&, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}&&)::{lambda(boost::context::fiber&&)#1}&, boost::context::fiber>(std::__invoke_other, boost::coroutines2::detail::pull_coroutine<int>::control_block::control_block<boost::context::basic_fixedsize_stack<boost::context::stack_traits>, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}>(boost::context::preallocated, boost::context::basic_fixedsize_stack<boost::context::stack_traits>&&, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}&&)::{lambda(boost::context::fiber&&)#1}&, boost::context::fiber&&) (__f=..., __args#0=...)  
    at .../8.2.0/include/g++-v8/bits/invoke.h:60  
#5  in std::__invoke<boost::coroutines2::detail::pull_coroutine<int>::control_block::control_block<boost::context::basic_fixedsize_stack<boost::context::stack_traits>, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}>(boost::context::preallocated, boost::context::basic_fixedsize_stack<boost::context::stack_traits>&&, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}&&)::{lambda(boost::context::fiber&&)#1}&, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}&&>(boost::coroutines2::detail::pull_coroutine<int>::control_block::control_block<boost::context::basic_fixedsize_stack<boost::context::stack_traits>, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}>(boost::context::preallocated, boost::context::basic_fixedsize_stack<boost::context::stack_traits>&&, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}&&)::{lambda(boost::context::fiber&&)#1}&, boost::coroutines2::detail::pull_coroutine<int>::control_block::control_block<boost::context::basic_fixedsize_stack<boost::context::stack_traits>, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}>(boost::context::preallocated, boost::context::basic_fixedsize_stack<boost::context::stack_traits>&&, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}&&)::{lambda(boost::context::fiber&&)#1}&) (__fn=..., __args#0=...)  
    at .../8.2.0/include/g++-v8/bits/invoke.h:96  
#6  in std::invoke<boost::coroutines2::detail::pull_coroutine<int>::control_block::control_block<boost::context::basic_fixedsize_stack<boost::context::stack_traits>, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}>(boost::context::preallocated, boost::context::basic_fixedsize_stack<boost::context::stack_traits>&&, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}&&)::{lambda(boost::context::fiber&&)#1}&, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}&&>(boost::coroutines2::detail::pull_coroutine<int>::control_block::control_block<boost::context::basic_fixedsize_stack<boost::context::stack_traits>, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}>(boost::context::preallocated, boost::context::basic_fixedsize_stack<boost::context::stack_traits>&&, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}&&)::{lambda(boost::context::fiber&&)#1}&, boost::coroutines2::detail::pull_coroutine<int>::control_block::control_block<boost::context::basic_fixedsize_stack<boost::context::stack_traits>, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}>(boost::context::preallocated, boost::context::basic_fixedsize_stack<boost::context::stack_traits>&&, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}&&)::{lambda(boost::context::fiber&&)#1}&) (__fn=..., __args#0=...)  
    at .../8.2.0/include/g++-v8/functional:82  
#7  in boost::context::detail::fiber_record<boost::context::fiber, boost::context::basic_fixedsize_stack<boost::context::stack_traits>, boost::coroutines2::detail::pull_coroutine<int>::control_block::control_block<boost::context::basic_fixedsize_stack<boost::context::stack_traits>, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}>(boost::context::preallocated, boost::context::basic_fixedsize_stack<boost::context::stack_traits>&&, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}&&)::{lambda(boost::context::fiber&&)#1}>::run(void*) (...)  
    at .../include/boost/context/fiber_fcontext.hpp:144  
#8  in boost::context::detail::fiber_entry<boost::context::detail::fiber_record<boost::context::fiber, boost::context::basic_fixedsize_stack<boost::context::stack_traits>, boost::coroutines2::detail::pull_coroutine<int>::control_block::control_block<boost::context::basic_fixedsize_stack<boost::context::stack_traits>, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}>(boost::context::preallocated, boost::context::basic_fixedsize_stack<boost::context::stack_traits>&&, make_coro()::{lambda(boost::coroutines2::detail::push_coroutine<int>&)#1}&&)::{lambda(boost::context::fiber&&)#1}> >(boost::context::detail::transfer_t) (t=...)  
    at .../include/boost/context/fiber_fcontext.hpp:80  
#9  in make_fcontext ()  
```

---

## Comment 1

> Username: olk  
> Created at: 2019-02-28 07:07:29 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/25#issuecomment-468162320  

your example works for me -> `program exited normally`  
I used branch develop (coroutine2 and context)

---

## Comment 2

> Username: alexeiz  
> Created at: 2019-02-28 18:47:42 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/25#issuecomment-468390242  

Program exits normally.  The issue is that the exception is thrown and then caught.  Do 'catch throw' in gdb and observe it.  There should be no exceptions at all.

---

## Comment 3

> Username: justinasvd  
> Created at: 2019-03-13 23:32:48 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/25#issuecomment-472646749  

`forced_unwind` is absolutely needed in general case. The example, however, poses the question whether it could be possible to optimize away unwinding in some specific scenarios (no local variables, simple function, etc.)  
  
In my experience coroutines are rarely as trivial as this one, and if state tracking to optimize away unwinding does not come for free, then it's not worth it. Because for most users coroutine completion would actually become a couple of instructions slower.

---

## Comment 4

> Username: alexeiz  
> Created at: 2019-03-14 15:26:59 UTC  
> Updated at: 2019-03-14 15:30:34 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/25#issuecomment-472918144  

I'd argue that `force_unwind` is only needed when there is an actual unwind to perform.  In this case coroutines finish cleanly, no unwind is needed.  So why do this unnecessary and expensive throw and catch an exception if it's not needed?  The cost of throwing exceptions makes the coroutine2 library unfitting for many applications that require predictable latency (exceptions don't have predictable latency).  
  
For comparison, the equivalent example using boost::fiber (which coroutine2 is based upon), would not lead to throwing and catching any exceptions.  It would be great if coroutine2 followed the same guidelines.

---

## Comment 5

> Username: olk  
> Created at: 2020-04-24 09:23:44 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/25#issuecomment-618904205  

fibers from boost.context do use only `forced_unwind` if destroyed while some stack frame remain on their associated stack. If a fiber simply returns from the top level function, not `forced_unwind` is thrown.  
But the design of boost.coroutine2 doesn't allow this at the moment.

---

## Comment 6

> Username: alexeiz  
> Created at: 2020-04-26 04:50:27 UTC  
> Updated at: 2020-04-26 04:50:35 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/25#issuecomment-619481605  

I hope you understand that this issue makes the performance of boost.coroutine2 unacceptable.
