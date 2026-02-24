# #3 universal-windows: GetProcessTimes is not supported [Merged]

> Username: mauve  
> Created at: 2015-10-07 13:18:46 UTC  
> Updated at: 2019-01-16 17:35:13 UTC  
> Merged at: 2019-01-16 15:53:52 UTC  
> Closed at: 2019-01-16 15:53:52 UTC  
> Url: https://github.com/boostorg/timer/pull/3  

While it might be possible to use GetProcAddress() to find the function (refer: http://stackoverflow.com/questions/12338953/is-there-any-way-for-a-winrt-app-to-measure-its-own-cpu-usage) you will not pass app certification when using that function so it might be safer to just not use it for now.

---

## Comment 1

> Username: jeking3  
> Created_at: 2017-10-23 15:07:21 UTC  
> Url: https://github.com/boostorg/timer/pull/3#issuecomment-338690169  

The Boost.WinAPI library contains a wrapper for this:  
https://github.com/boostorg/winapi/blob/develop/include/boost/winapi/get_process_times.hpp  
  
In particular, `::GetProcessTimes` is available in different WINAPI_FAMILY(ies) in different MSVC releases.  
If you switch to using Boost.WinAPI and guard it with BOOST_WINAPI_PARTITION_APP_SYSTEM, and call ::boost::winapi::GetProcessTimes, you can call the method without including <Windows.h>.

---

## Comment 2

> Username: pdimov  
> Created_at: 2019-01-15 19:38:38 UTC  
> Url: https://github.com/boostorg/timer/pull/3#issuecomment-454523103  

For some reason, when I apply this PR, the test `cpu_timer_info` starts hanging under Cygwin, and I have no idea why. Apparently, `cpu.elapsed().wall` never changes, but what causes it, is a mystery, as previous runs did not exhibit this behavior, and as far as I see, nothing in Chrono changed either.

---

## Comment 3

> Username: pdimov  
> Created_at: 2019-01-15 23:17:14 UTC  
> Url: https://github.com/boostorg/timer/pull/3#issuecomment-454589047  

Never mind, it's actually `cpu.elapsed().user` that doesn't change, due to `BOOST_PLAT_WINDOWS_DESKTOP` being 0 for Cygwin.

---

## Comment 4

> Username: jeking3  
> Created_at: 2019-01-16 17:25:46 UTC  
> Url: https://github.com/boostorg/timer/pull/3#issuecomment-454865975  

Does this repository have CI?

---

## Comment 5

> Username: pdimov  
> Created_at: 2019-01-16 17:35:13 UTC  
> Url: https://github.com/boostorg/timer/pull/3#issuecomment-454869329  

Yes.

---
