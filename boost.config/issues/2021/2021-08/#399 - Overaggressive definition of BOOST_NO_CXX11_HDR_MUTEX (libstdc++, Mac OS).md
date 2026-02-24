# #399 - Overaggressive definition of BOOST_NO_CXX11_HDR_MUTEX (libstdc++, Mac OS) [Closed]

> Username: francoisk  
> Created at: 2021-08-24 10:01:14 UTC  
> Updated at: 2022-11-09 12:51:57 UTC  
> Closed at: 2022-11-09 12:51:57 UTC  
> Url: https://github.com/boostorg/config/issues/399  

Boost.Config defines `BOOST_NO_CXX11_HDR_MUTEX`  if libstdc++ does not support timed mutexes (which is currently the case on Mac OS because `_POSIX_TIMEOUTS` is `-1` on that platform).  
  
But there are many other useful constructs in `<mutex>` besides `timed_mutex`.  
  
This also causes other Boost libraries to make drastic decisions such as Boost.Regex going into C++03 mode and selecting its v4 instead of v5 headers, and Boost.Context using Boost.Thread instead of `<mutex>`, despite the fact that neither of these libraries even use any timed mutexes (as far as I can tell).  
  
Does this not seem a bit too aggressive?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-09-10 10:52:29 UTC  
> Url: https://github.com/boostorg/config/issues/399#issuecomment-916813853  

BOOST_NO_CXX11_HDR_MUTEX is an all or nothing macro - and timed mutexes are not an optional part of that header.  
  
However, looking at the latest MacOS runs here: https://github.com/boostorg/config/runs/3565734434?check_suite_focus=true, and looking at the config_info output in C++11 mode or later, I see that BOOST_NO_CXX11_HDR_MUTEX is *NOT* defined.  This is with "Apple LLVM 12.0.0 (clang-1200.0.32.29)" BTW.

---

## Comment 2

> Username: francoisk  
> Created at: 2021-09-16 06:37:04 UTC  
> Url: https://github.com/boostorg/config/issues/399#issuecomment-920619446  

> BOOST_NO_CXX11_HDR_MUTEX is an all or nothing macro - and timed mutexes are not an optional part of that header.  
  
You could add a finer-grained macro like BOOST_NO_CXX11_TIMED_MUTEX. This is what libstdc++ appears to have done: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=50196#c1. However I hear you about `<mutex>` being a complete unit.  
  
> However, looking at the latest MacOS runs here: https://github.com/boostorg/config/runs/3565734434 check_suite_focus=true, and looking at the config_info output in C++11 mode or later, I see that BOOST_NO_CXX11_HDR_MUTEX is NOT defined. This is with "Apple LLVM 12.0.0 (clang-1200.0.32.29)" BTW.  
  
Right, the problem is only with libstdc++ on Mac OS.

---

## Comment 3

> Username: barracuda156  
> Created at: 2022-10-19 04:45:02 UTC  
> Url: https://github.com/boostorg/config/issues/399#issuecomment-1283420556  

This should be fixed. As it is, it breaks the build with GCC: https://github.com/boostorg/json/issues/774

---

## Comment 4

> Username: pdimov  
> Created at: 2022-10-19 07:59:12 UTC  
> Updated at: 2022-10-19 08:01:46 UTC  
> Url: https://github.com/boostorg/config/issues/399#issuecomment-1283589090  

Yeah, Boost.System disables `<system_error>` interoperability when `BOOST_NO_CXX11_HDR_MUTEX` is defined, because it needs to use `std::mutex`. I'm not sure why timed mutexes are deemed so relevant as to disable the entire header, but even if we suppose they are for some reason, how can I recover from this? There's no way to know that Boost.Config has decided to disable `<mutex>` support because of the absence of timed mutexes, and I don't want to drop my support for `<system_error>` in that case.

---

## Comment 5

> Username: pdimov  
> Created at: 2022-10-19 10:50:24 UTC  
> Url: https://github.com/boostorg/config/issues/399#issuecomment-1283808475  

The amusing thing is that libstdc++ actually seems to have a `timed_mutex` even when `_GTHREAD_USE_MUTEX_TIMEDLOCK` is not defined since [this 2015 commit](https://github.com/gcc-mirror/gcc/commit/f16081c2c6fb60a69195c3810fd88ed263757d21) which seems to be in GCC 6. So Config is totally in the wrong here, not just kind of wrong.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2022-10-19 11:04:31 UTC  
> Updated at: 2022-10-19 11:05:19 UTC  
> Url: https://github.com/boostorg/config/issues/399#issuecomment-1283830855  

@pdimov : care to file a PR?  
  
Or we could have a separate macro for \<system_error\> ?  
  
BTW if a check for an incomplete \<mutex\> is good enough then using __has_include plus a check on C++ version is probably good enough?  I'm confused why you're checking for \<mutex\> in that case?

---

## Comment 7

> Username: pdimov  
> Created at: 2022-10-19 11:24:46 UTC  
> Url: https://github.com/boostorg/config/issues/399#issuecomment-1283854600  

I don't understand your questions, sorry.

---

## Comment 8

> Username: pdimov  
> Created at: 2022-10-19 11:33:26 UTC  
> Url: https://github.com/boostorg/config/issues/399#issuecomment-1283864710  

I am checking the macro `BOOST_NO_CXX11_HDR_MUTEX`  
  
https://github.com/boostorg/system/blob/9a6d79b84147854aa919644b56b8553f5a2bedb8/include/boost/system/detail/config.hpp#L22  
  
because I need to include the `<mutex>` header  
  
https://github.com/boostorg/system/blob/9a6d79b84147854aa919644b56b8553f5a2bedb8/include/boost/system/detail/error_category_impl.hpp#L104  
  
and then use `std::mutex`  
  
https://github.com/boostorg/system/blob/9a6d79b84147854aa919644b56b8553f5a2bedb8/include/boost/system/detail/error_category_impl.hpp#L119  
  
The problem is that `BOOST_NO_CXX11_HDR_MUTEX` is getting defined for libstdc++ on MacOS here  
  
https://github.com/boostorg/config/blob/2543ff139707a32cc421b62c9d65bb4d9c965b3e/include/boost/config/stdlib/libstdcpp3.hpp#L464  
  
even though it has a `<mutex>` header, with a functional `std::mutex`.  
  
Furthermore, it looks like libstdc++ since GCC 6 has a fully functional `<mutex>` on MacOS, complete with timed mutexes.

---

## Comment 9

> Username: jzmaddock  
> Created at: 2022-10-19 14:42:15 UTC  
> Url: https://github.com/boostorg/config/issues/399#issuecomment-1284128962  

Got it.  Sorry I thought you were only use \<system_error\>.  
  
I'm away travelling for a few days now, but I'll try and get this when I get back.

---

## Comment 10

> Username: grisumbras  
> Created at: 2022-11-05 14:14:13 UTC  
> Url: https://github.com/boostorg/config/issues/399#issuecomment-1304554276  

Hi, any updates on this?

---

## Comment 11

> Username: pdimov  
> Created at: 2022-11-06 15:16:26 UTC  
> Url: https://github.com/boostorg/config/issues/399#issuecomment-1304824192  

Ping? Can we please get this fixed for 1.81?

---

## Comment 12

> Username: barracuda156  
> Created at: 2022-11-06 18:11:21 UTC  
> Url: https://github.com/boostorg/config/issues/399#issuecomment-1304860821  

> Ping? Can we please get this fixed for 1.81?  
  
And hopefully backported to 1.80, so we don’t end up with one version of Boost broken on a bunch of systems.

---

## Comment 13

> Username: jzmaddock  
> Created at: 2022-11-07 19:16:57 UTC  
> Url: https://github.com/boostorg/config/issues/399#issuecomment-1306074988  

OK the referenced PR is nearly there, but there are testing failures with gcc-11 on MacOS 12: https://github.com/boostorg/config/actions/runs/3412675779/jobs/5678491487, anyone able to figure out what the issue is?  Gcc-11 on MacOS 11 is fine BTW.

---

## Comment 14

> Username: pdimov  
> Created at: 2022-11-07 19:45:40 UTC  
> Url: https://github.com/boostorg/config/issues/399#issuecomment-1306102855  

Looks like it's just crashing without any other information. It's probably a preexisting problem not caused by the patch.

---

## Comment 15

> Username: jzmaddock  
> Created at: 2022-11-08 13:16:36 UTC  
> Url: https://github.com/boostorg/config/issues/399#issuecomment-1307205757  

>Looks like it's just crashing without any other information. It's probably a preexisting problem not caused by the patch.  
  
Oh sure, it would just be nice to fix things while I'm here.
