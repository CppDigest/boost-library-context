# #263 Fix compilation of timed functions on Cygwin [Merged]

> Username: Lastique  
> Created at: 2019-01-06 14:57:52 UTC  
> Updated at: 2019-01-19 20:07:00 UTC  
> Merged at: 2019-01-06 19:48:10 UTC  
> Closed at: 2019-01-06 19:48:10 UTC  
> Url: https://github.com/boostorg/thread/pull/263  

Cygwin includes both Windows and POSIX API and, depending on the included headers, may have WIN32 macros defined. Since Boost.Thread uses pthread API on Cygwin, it should also enable POSIX API for time units (i.e. timespec).

---

## Comment 1

> Username: viboes  
> Created_at: 2019-01-06 17:43:40 UTC  
> Url: https://github.com/boostorg/thread/pull/263#issuecomment-451760051  

We should the same as we do for Boost.Chrono, isn't it?

---

## Comment 2

> Username: Lastique  
> Created_at: 2019-01-06 17:57:00 UTC  
> Url: https://github.com/boostorg/thread/pull/263#issuecomment-451760998  

What do you mean?

---

## Comment 3

> Username: pdimov  
> Created_at: 2019-01-06 18:40:30 UTC  
> Url: https://github.com/boostorg/thread/pull/263#issuecomment-451764071  

This should probably use whatever actual threading API has been selected (via `b2 threadapi=...`).

---

## Comment 4

> Username: Lastique  
> Created_at: 2019-01-06 18:48:45 UTC  
> Url: https://github.com/boostorg/thread/pull/263#issuecomment-451764633  

That feature is currently unrelated to `BOOST_THREAD_CHRONO*` macros, which seems to be a configuration point of their own. The problem is that on Cygwin the library uses pthread by default, but doesn't compile the `timespec`-related glue, which breaks the compilation.

---

## Comment 5

> Username: viboes  
> Created_at: 2019-01-06 18:54:42 UTC  
> Url: https://github.com/boostorg/thread/pull/263#issuecomment-451765083  

Please, could you report the compilation error?

---

## Comment 6

> Username: viboes  
> Created_at: 2019-01-06 18:56:37 UTC  
> Url: https://github.com/boostorg/thread/pull/263#issuecomment-451765260  

I believe that originally even on Cygwin Boost.Thread didn't used the pthread api.  
I don't remember since when it has been moved to use by default the pthread API.

---

## Comment 7

> Username: pdimov  
> Created_at: 2019-01-06 18:59:56 UTC  
> Url: https://github.com/boostorg/thread/pull/263#issuecomment-451765479  

What compilation does it break? I'm pretty sure we had the tests passing under Cygwin at one point.  
  
The default under Cygwin is pthread, but you _can_ use `threadapi=win32`, and I think I got the tests passing under that configuration too (https://github.com/boostorg/thread/pull/244).

---

## Comment 8

> Username: Lastique  
> Created_at: 2019-01-06 19:05:23 UTC  
> Url: https://github.com/boostorg/thread/pull/263#issuecomment-451765891  

It breaks Boost.Log with errors like this:  
  
```  
In file included from ./boost/thread/recursive_mutex.hpp:16:0,  
                 from ./boost/log/sinks/sync_frontend.hpp:33,  
                 from ./boost/log/utility/setup/file.hpp:32,  
                 from libs/log/test/compile/self_contained_header.cpp:17:  
./boost/thread/pthread/recursive_mutex.hpp: In member function вЂbool boost::recursive_timed_mutex::do_try_lock_until(const internal_platform_timepoint&)вЂ™:  
./boost/thread/pthread/recursive_mutex.hpp:350:77: error: вЂconst internal_platform_timepoint {aka const struct boost::detail::mono_platform_timepoint}вЂ™ has no member named вЂgetTsвЂ™; did you mean вЂgetNsвЂ™?  
                 int const cond_res=pthread_cond_timedwait(&cond,&m,&timeout.getTs());  
                                                                             ^~~~~  
                                                                             getNs  
```  
  
I've added a commit to select the timing API based on the threading API.

---

## Comment 9

> Username: pdimov  
> Created_at: 2019-01-06 21:41:14 UTC  
> Url: https://github.com/boostorg/thread/pull/263#issuecomment-451777471  

I tried the tests under Cygwin (before this patch), and they all pass both with `threadapi=win32` and without. So this issue had no test coverage, FWIW.

---

## Comment 10

> Username: Lastique  
> Created_at: 2019-01-17 14:51:14 UTC  
> Url: https://github.com/boostorg/thread/pull/263#issuecomment-455198619  

Could this fix be merged to master, please?

---

## Comment 11

> Username: viboes  
> Created_at: 2019-01-18 22:15:58 UTC  
> Url: https://github.com/boostorg/thread/pull/263#issuecomment-455705553  

I suspect that Peter is asking for a test case that probes there is an issue, isn't it?

---

## Comment 12

> Username: viboes  
> Created_at: 2019-01-19 08:01:28 UTC  
> Url: https://github.com/boostorg/thread/pull/263#issuecomment-455758928  

Andrey, please, could you provide a test case?

---

## Comment 13

> Username: viboes  
> Created_at: 2019-01-19 20:07:00 UTC  
> Url: https://github.com/boostorg/thread/pull/263#issuecomment-455811126  

Thanks, I will do the merge as soon as possible.

---
