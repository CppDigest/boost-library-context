# #123 Fix entry_manager constructor not accepting entry_ptr rvalue [Merged]

> Username: Lastique  
> Created at: 2017-05-22 10:12:20 UTC  
> Updated at: 2017-05-25 08:06:53 UTC  
> Merged at: 2017-05-23 19:54:31 UTC  
> Closed at: 2017-05-23 19:54:31 UTC  
> Url: https://github.com/boostorg/thread/pull/123  

This resolves compilation failures with MinGW.

---

## Comment 1

> Username: viboes  
> Created_at: 2017-05-23 19:54:19 UTC  
> Url: https://github.com/boostorg/thread/pull/123#issuecomment-303513268  

Thanks for this PR.   
I can not test it.   
Hoping that everything that work now will continue to work I will merge it. We could rollback if it introduce some regressions.

---

## Comment 2

> Username: Lastique  
> Created_at: 2017-05-23 19:59:09 UTC  
> Url: https://github.com/boostorg/thread/pull/123#issuecomment-303514500  

You can see the test results on the regression page:  
  
http://www.boost.org/development/tests/develop/developer/thread.html  
  
Currently, most MinGW testers fail because of this bug like this:  
  
```  
In file included from ../boost/thread/condition_variable.hpp:14:0,  
                 from ../boost/thread/future.hpp:20,  
                 from ..\libs\thread\test\sync\futures\async\async_executor_pass.cpp:28:  
../boost/thread/win32/condition_variable.hpp: In instantiation of 'bool boost::detail::basic_condition_variable::do_wait(lock_type&, boost::detail::timeout) [with lock_type = boost::unique_lock<boost::mutex>]':  
../boost/thread/win32/condition_variable.hpp:332:50:   required from here  
../boost/thread/win32/condition_variable.hpp:233:67: error: no matching function for call to 'boost::detail::basic_condition_variable::entry_manager::entry_manager(boost::detail::basic_condition_variable::entry_ptr, boost::mutex&)'  
```  
  
http://www.boost.org/development/tests/develop/developer/output/igaztanaga-develop-gcc-4-7c++11-boost-bin-v2-libs-thread-test-async__async_executor_p-test-gcc-mngw-4-7c+-dbg-dbg-symbl-off-thrd-mlt.html  
  
In my local tests on MinGW the problem was fixed with this PR.

---

## Comment 3

> Username: viboes  
> Created_at: 2017-05-23 20:45:50 UTC  
> Url: https://github.com/boostorg/thread/pull/123#issuecomment-303526803  

What about the other platforms and compilers?

---

## Comment 4

> Username: Lastique  
> Created_at: 2017-05-23 21:32:18 UTC  
> Url: https://github.com/boostorg/thread/pull/123#issuecomment-303538495  

This is a Windows-only change. I did not test MSVC, but I'm pretty sure it should be fine.

---

## Comment 5

> Username: viboes  
> Created_at: 2017-05-23 22:04:32 UTC  
> Url: https://github.com/boostorg/thread/pull/123#issuecomment-303545846  

I was thinking in Windows. Antony has promise to test it tome. We will see on the regression test also.  
  
Thanks.

---

## Comment 6

> Username: viboes  
> Created_at: 2017-05-25 08:06:53 UTC  
> Url: https://github.com/boostorg/thread/pull/123#issuecomment-303950094  

It seems this is working well.   
  
Thanks again,  
Vicente

---
