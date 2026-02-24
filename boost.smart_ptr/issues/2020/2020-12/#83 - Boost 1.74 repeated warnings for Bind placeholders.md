# #83 - Boost 1.74 repeated warnings for Bind placeholders [Closed]

> Username: neel  
> Created at: 2020-12-14 11:18:30 UTC  
> Updated at: 2020-12-14 18:24:49 UTC  
> Closed at: 2020-12-14 18:24:49 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/83  

Since update to boost 1.74 I am getting repeated warnings like the following.  
  
```  
In file included from /usr/include/boost/smart_ptr/detail/sp_thread_sleep.hpp:22,  
                 from /usr/include/boost/smart_ptr/detail/yield_k.hpp:23,  
                 from /usr/include/boost/smart_ptr/detail/spinlock_gcc_atomic.hpp:14,  
                 from /usr/include/boost/smart_ptr/detail/spinlock.hpp:42,  
                 from /usr/include/boost/smart_ptr/detail/spinlock_pool.hpp:25,  
                 from /usr/include/boost/smart_ptr/shared_ptr.hpp:29,  
                 from /usr/include/boost/shared_ptr.hpp:17,  
                 from /usr/include/boost/date_time/time_clock.hpp:17,  
                 from /usr/include/boost/date_time/posix_time/posix_time_types.hpp:10,  
                 from /usr/include/boost/asio/time_traits.hpp:23,  
                 from /usr/include/boost/asio/detail/timer_queue_ptime.hpp:22,  
                 from /usr/include/boost/asio/detail/deadline_timer_service.hpp:29,  
                 from /usr/include/boost/asio/basic_deadline_timer.hpp:25,  
                 from /usr/include/boost/asio.hpp:25,  
                 from /home/neel/Projects/udho/examples/hello.cpp:2:  
/usr/include/boost/bind.hpp:36:1: note: ‘#pragma message: The practice of declaring the Bind placeholders (_1, _2, ...) in the global namespace is deprecated. Please use <boost/bind/bind.hpp> + using namespace boost::placeholders, or define BOOST_BIND_GLOBAL_PLACEHOLDERS to retain the current behavior.’  
```  
  
In the `hello.cpp` mentioned in the error message I do not use `boost::bind`. Neither did I find any instance of using `_1` along with `boost::bind` without a namespace in my code. I suppose the warning is coming from some other boost library. But I could not find the source.

---

## Comment 1

> Username: pdimov  
> Created at: 2020-12-14 14:22:08 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/83#issuecomment-744472731  

Line 22 in sp_thread_sleep.hpp is `#include <boost/config/pragma_message.hpp>`, so this is not where the Bind warning comes from. There must be more to the note message that tells us where `boost/bind.hpp` is getting included.  
  
You can disable these warnings by defining `BOOST_BIND_GLOBAL_PLACEHOLDERS` for your project.

---

## Comment 2

> Username: pdimov  
> Created at: 2020-12-14 14:23:54 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/83#issuecomment-744473895  

Also, you can try with Boost 1.75, and see if the problem has been fixed.

---

## Comment 3

> Username: neel  
> Created at: 2020-12-14 14:27:06 UTC  
> Updated at: 2020-12-14 14:30:02 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/83#issuecomment-744475989  

@pdimov I am using 1.75 now after arch update. The issue persists. `BOOST_BIND_GLOBAL_PLACEHOLDERS` is not a solution for long term. Currently these are warnings , and I am not using `-Werror`. So I can sustain this issue. But its annoying to get lost in too many messages.

---

## Comment 4

> Username: pdimov  
> Created at: 2020-12-14 14:29:23 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/83#issuecomment-744477474  

Is your `hello.cpp` available?

---

## Comment 5

> Username: neel  
> Created at: 2020-12-14 14:31:13 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/83#issuecomment-744478701  

@pdimov Yes sure here is it.  
  
https://gitlab.com/neel.basu/udho/-/blob/develop/examples/hello.cpp

---

## Comment 6

> Username: pdimov  
> Created at: 2020-12-14 15:16:22 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/83#issuecomment-744508545  

Your `udho/connection.h` header includes `boost/bind.hpp`, which causes the warning. But you don't use `boost::bind`, you use `std::bind` in the file, so you can just remove the include.

---

## Comment 7

> Username: neel  
> Created at: 2020-12-14 15:32:57 UTC  
> Updated at: 2020-12-14 15:33:42 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/83#issuecomment-744519178  

@pdimov Yes thanks for mentioning about the unused include. In some other places too I had included `bind.hpp` but I never used it. Removing those includes solved the warning issues.  
  
But why would it show warning messages even when I am not using it ?

---

## Comment 8

> Username: pdimov  
> Created at: 2020-12-14 16:41:07 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/83#issuecomment-744563163  

It's because `boost/bind.hpp` contains `using namespace boost::placeholders;`, and using directives in headers are bad practice. We want to remove this at some point, but since it will break code, there's a warning to get people to migrate to using `boost/bind/bind.hpp`, which will still work in the future.  
  
It's not possible to issue a warning at the point of use in this case.
