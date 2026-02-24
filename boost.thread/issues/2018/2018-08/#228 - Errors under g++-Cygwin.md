# #228 - Errors under g++/Cygwin [Closed]

> Username: pdimov  
> Created at: 2018-08-28 21:48:34 UTC  
> Updated at: 2018-10-25 01:57:40 UTC  
> Closed at: 2018-10-13 02:01:30 UTC  
> Url: https://github.com/boostorg/thread/issues/228  

I'm running the tests for Boost.Pool under g++/Cygwin, and this is what I get:  
  
```  
In file included from ..\../boost/thread/win32/recursive_mutex.hpp:13:0,  
                 from ..\../boost/thread/recursive_mutex.hpp:14,  
                 from ..\../boost/thread.hpp:18,  
                 from test\test_threading.cpp:10:  
..\../boost/thread/win32/basic_recursive_mutex.hpp: In member function 'bool boo  
st::detail::basic_recursive_mutex_impl<underlying_mutex_type>::timed_lock(const  
Duration&)':  
..\../boost/thread/win32/basic_recursive_mutex.hpp:70:61: error: 'boost::detail:  
:winapi' has not been declared  
                 long const current_thread_id=boost::detail::winapi::GetCurrentT  
hreadId();  
                                                             ^~~~~~  
..\../boost/thread/win32/basic_recursive_mutex.hpp: In member function 'bool boo  
st::detail::basic_recursive_mutex_impl<underlying_mutex_type>::try_lock_for(cons  
t boost::chrono::duration<Rep2, Period2>&)':  
..\../boost/thread/win32/basic_recursive_mutex.hpp:79:61: error: 'boost::detail:  
:winapi' has not been declared  
                 long const current_thread_id=boost::detail::winapi::GetCurrentT  
hreadId();  
                                                             ^~~~~~  
..\../boost/thread/win32/basic_recursive_mutex.hpp: In member function 'bool boo  
st::detail::basic_recursive_mutex_impl<underlying_mutex_type>::try_lock_until(co  
nst boost::chrono::time_point<Clock, Duration>&)':  
..\../boost/thread/win32/basic_recursive_mutex.hpp:85:61: error: 'boost::detail:  
:winapi' has not been declared  
                 long const current_thread_id=boost::detail::winapi::GetCurrentT  
hreadId();  
                                                             ^~~~~~  
```  
  
This should presumably be `boost::winapi`?  
  
Also, there's this:  
  
```  
..\..\libs\thread\src\win32\thread.cpp: In function 'void boost::{anonymous}::cr  
eate_current_thread_tls_key()':  
..\..\libs\thread\src\win32\thread.cpp:86:13: error: 'tss_cleanup_implemented' w  
as not declared in this scope  
             tss_cleanup_implemented(); // if anyone uses TSS, we need the clean  
up linked in  
             ^~~~~~~~~~~~~~~~~~~~~~~  
```  
  
Additionally, with `cxxstd=03` I get these:  
  
```  
In file included from ..\../boost/thread/detail/config.hpp:14:0,  
                 from ..\../boost/thread/win32/thread_data.hpp:9,  
                 from ..\../boost/thread/thread_only.hpp:15,  
                 from ..\..\libs\thread\src\win32\thread.cpp:11:  
..\../boost/thread/detail/thread_safety.hpp:26:38: warning: anonymous variadic m  
acros were introduced in C++11 [-Wvariadic-macros]  
 #define BOOST_THREAD_ACQUIRED_BEFORE(...) \  
                                      ^~~  
..\../boost/thread/detail/thread_safety.hpp:29:37: warning: anonymous variadic m  
acros were introduced in C++11 [-Wvariadic-macros]  
 #define BOOST_THREAD_ACQUIRED_AFTER(...) \  
                                     ^~~  
```  
...  
```  
In file included from ..\../boost/thread/win32/condition_variable.hpp:19:0,  
                 from ..\../boost/thread/condition_variable.hpp:14,  
                 from ..\../boost/thread/thread_only.hpp:26,  
                 from ..\..\libs\thread\src\win32\thread.cpp:11:  
..\../boost/thread/lock_guard.hpp:65:40: warning: invoking macro BOOST_THREAD_RE  
LEASE argument 1: empty macro arguments are undefined in ISO C++98 [-Wpedantic]  
     ~lock_guard() BOOST_THREAD_RELEASE()  
                                        ^  
```

---

## Comment 1

> Username: viboes  
> Created at: 2018-09-06 05:06:31 UTC  
> Url: https://github.com/boostorg/thread/issues/228#issuecomment-418965947  

Hi Peter,   
  
The variadic and empty macros come from a PR I accepted long time ago. Do you know how to silent those warnings at the source level. Patch welcome.  
  
For winapi, Andrei did a move to boost::winapi. Maybe this is still missing.  
Andrei, maybe you could help.  
  
For `tss_cleanup_implemented`I never understood that.  
  
I don't have time to look at this just now.   
  
Individual patches to fix any of this are welcome.

---

## Comment 2

> Username: viboes  
> Created at: 2018-10-09 04:42:32 UTC  
> Url: https://github.com/boostorg/thread/issues/228#issuecomment-428057621  

The warning should be fixed now (using system header).  
  
For the tss_cleanup_implemented I have no idea.  
  
Any PR will be more than welcome

---

## Comment 3

> Username: pdimov  
> Created at: 2018-10-11 13:17:01 UTC  
> Url: https://github.com/boostorg/thread/issues/228#issuecomment-428951221  

The `tss_cleanup_implemented` error seems to have two causes. First, under Cygwin, `threadapi=win32` is chosen, whereas it should be `pthread` (`target-os` is `cygwin`, not `windows`.) I haven't been able to track down _why_ it's chosen, will open an issue in Build.  
  
Second, `tss_hooks.hpp` only declares `tss_cleanup_implemented` when `BOOST_HAS_WINTHREADS` is defined, but the `win32` sources are included on the basis of `threadapi`, which in this case does not correspond to whether `BOOST_HAS_WINTHREADS` is defined. The Jamfile defines `BOOST_THREAD_WIN32` on `threadapi=win32`, which is what presumably is the correct macro here.

---

## Comment 4

> Username: viboes  
> Created at: 2018-10-11 22:22:26 UTC  
> Url: https://github.com/boostorg/thread/issues/228#issuecomment-429139487  

I don't know why, but Boost.Thread decided that under Cygwin the thread api is windows and not posix :(  
Maybe Anthony remember why. I believe it was because it can and more services and more efficient are available.   
  
Note that for Boost.Chrono, this is not the case, and I was asking to change this when Anthony maintained Boost.Thread.

---

## Comment 5

> Username: viboes  
> Created at: 2018-10-11 22:28:22 UTC  
> Url: https://github.com/boostorg/thread/issues/228#issuecomment-429141158  

Peter, do you plan to provide a patch for this issue after #352 patch?

---

## Comment 6

> Username: pdimov  
> Created at: 2018-10-11 22:34:06 UTC  
> Url: https://github.com/boostorg/thread/issues/228#issuecomment-429142622  

After https://github.com/boostorg/build/issues/352 is resolved, Thread should build on Cygwin as `threadapi=pthread` will be default.  
  
Making it build with `threadapi=win32` on Cygwin might also be possible, but I'm not sure if it'll be worth the effort.

---

## Comment 7

> Username: pdimov  
> Created at: 2018-10-12 01:02:53 UTC  
> Url: https://github.com/boostorg/thread/issues/228#issuecomment-429169934  

After https://github.com/boostorg/build/commit/a5704a926bc7bc69311d472bb97f7a80c53d017a, Thread's tests now seem to work for me with `toolset=gcc` and Cygwin.

---

## Comment 8

> Username: viboes  
> Created at: 2018-10-13 00:07:50 UTC  
> Url: https://github.com/boostorg/thread/issues/228#issuecomment-429493667  

Great. Glad to see that it works again, after so many years.  
  
Peter, is there something to fix for this ticket yet?  
If no, maybe you can close it.

---

## Comment 9

> Username: pdimov  
> Created at: 2018-10-13 02:00:55 UTC  
> Url: https://github.com/boostorg/thread/issues/228#issuecomment-429502465  

We can also fix the `threadapi=win32` case under Cygwin, see #244. With that patch, there's only one failure, `test_tss_lib` says there's no TSS cleanup in the static lib case, which there indeed isn't.  
  
If you want to support Cygwin going forward, it might be worth adding it to Appveyor.  
  
Apart from that, it looks like we're done here.

---

## Comment 10

> Username: viboes  
> Created at: 2018-10-16 19:51:22 UTC  
> Url: https://github.com/boostorg/thread/issues/228#issuecomment-430376057  

Do you have an example of setup for Appveyor? or even a PR would be better :)

---

## Comment 11

> Username: pdimov  
> Created at: 2018-10-16 22:18:45 UTC  
> Url: https://github.com/boostorg/thread/issues/228#issuecomment-430421038  

Boost.System has Cygwin and Cygwin 64 configurations: https://github.com/boostorg/system/blob/develop/appveyor.yml#L26

---

## Comment 12

> Username: viboes  
> Created at: 2018-10-25 01:57:39 UTC  
> Url: https://github.com/boostorg/thread/issues/228#issuecomment-432886684  

Thanks
