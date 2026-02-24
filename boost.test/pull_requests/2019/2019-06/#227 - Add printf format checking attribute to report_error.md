# #227 Add printf format checking attribute to report_error [Closed]

> Username: ecatmur  
> Created at: 2019-06-14 10:22:28 UTC  
> Updated at: 2019-10-21 18:29:16 UTC  
> Closed at: 2019-10-21 18:29:08 UTC  
> Url: https://github.com/boostorg/test/pull/227  

On gcc and clang, add `__attribute__((__format__))` checking to the `report_error` function.  
  
Note: clang warns `-Wformat-nonliteral` (off by default, but good practice) when a format string argument not annotated by `__attribute__((__format__))` is passed to `vprintf` etc.; gcc does not. See https://clang.llvm.org/docs/AttributeReference.html#format .  
  
Plus fixes for bugs exposed by this change:  
* Cast faulting addresses to `uintptr_t` for formatting as `0x%08lx`. If there is an LLP64 platform that uses Posix signals (i.e. not Win64, which uses SEH) this should be changed to use `PRIxPTR` format specifier.  
* Fix swapped `si_code`/`si_addr` (& `si_band`) format arguments.  
* Add missing `%s` to format diagnostic information.

---

## Review 1 [Commented]

> Username: raffienficiaud  
> Created_at: 2019-07-21 12:04:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/test/pull/227#pullrequestreview-264533570  

📁 include/boost/test/impl/execution_monitor.ipp

```diff
1309 | #else
1301 |-                               boost::diagnostic_information(ex).c_str() ); }
1310 |+                               "%s", boost::diagnostic_information(ex).c_str() ); }
```

> Username: raffienficiaud  
> Created_at: 2019-07-21 12:04:43 UTC  
> Updated_at: 2019-07-22 14:17:33 UTC  
> Url: https://github.com/boostorg/test/pull/227#discussion_r305614715  

I am not sure to understand why this change would be needed.

> Username: ecatmur  
> Created_at: 2019-07-22 11:35:37 UTC  
> Updated_at: 2019-07-22 14:17:33 UTC  
> Url: https://github.com/boostorg/test/pull/227#discussion_r305798271  

It's a security issue as well as a correctness issue:  
  
    gcc.compile.c++ ../../../bin.v2/libs/test/build/gcc-7/debug/link-static/threading-multi/visibility-hidden/execution_monitor.o  
    In file included from ../../../libs/test/src/execution_monitor.cpp:16:0:  
    ../../../boost/test/impl/execution_monitor.ipp: In member function ‘int boost::execution_monitor::execute(const boost::function<int()>&)’:  
    ../../../boost/test/impl/execution_monitor.ipp:1311:73: error: format not a string literal and no format arguments [-Werror=format-security]  
                                   boost::diagnostic_information(ex).c_str() ); }  
                                                                             ^  
    cc1plus: some warnings being treated as errors  
  
I'll add a test illustrating this.


---

## Review 2 [Commented]

> Username: raffienficiaud  
> Created_at: 2019-07-21 12:05:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/test/pull/227#pullrequestreview-264533597  

📁 include/boost/test/impl/execution_monitor.ipp

```diff
 555 |                           "memory access violation at address: 0x%08lx: object specific hardware error",
 547 |-                           m_sig_info->si_addr );
 556 |+                           (uintptr_t) m_sig_info->si_addr );
```

> Username: raffienficiaud  
> Created_at: 2019-07-21 12:05:15 UTC  
> Updated_at: 2019-07-22 14:17:33 UTC  
> Url: https://github.com/boostorg/test/pull/227#discussion_r305614740  

`uintptr_t` might be unavailable on some compilers

> Username: ecatmur  
> Created_at: 2019-07-22 11:50:14 UTC  
> Updated_at: 2019-07-22 14:17:33 UTC  
> Url: https://github.com/boostorg/test/pull/227#discussion_r305803327  

You're already using `uintptr_t` in this file:   
https://github.com/boostorg/test/blob/4f3806d80252081bc87e71d97d5b0c8dfd063e17/include/boost/test/impl/execution_monitor.ipp#L1131  
https://github.com/boostorg/test/blob/4f3806d80252081bc87e71d97d5b0c8dfd063e17/include/boost/test/impl/execution_monitor.ipp#L87  
https://github.com/boostorg/test/blob/4f3806d80252081bc87e71d97d5b0c8dfd063e17/include/boost/test/impl/execution_monitor.ipp#L91


---

## Review 3 [Commented]

> Username: raffienficiaud  
> Created_at: 2019-07-21 12:06:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/test/pull/227#pullrequestreview-264533635  

📁 include/boost/test/impl/execution_monitor.ipp

```diff
 244 | static void
 245 |+ #ifdef __GNUC__
 246 |+ __attribute__((__format__ (__printf__, 3, 0)))
```

> Username: raffienficiaud  
> Created_at: 2019-07-21 12:06:18 UTC  
> Updated_at: 2019-07-22 14:17:33 UTC  
> Url: https://github.com/boostorg/test/pull/227#discussion_r305614778  

How can we ensure that this attribute is available for the target compiler?

> Username: ecatmur  
> Created_at: 2019-07-22 14:32:14 UTC  
> Url: https://github.com/boostorg/test/pull/227#discussion_r305877088  

I'll change the check to `__GNUC__ >= 3`. Or would you prefer to set this only for specific compilers (I've tested with gcc 6-8 and clang 5-9)?


---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2019-07-21 12:06:45 UTC  
> Url: https://github.com/boostorg/test/pull/227#issuecomment-513547961  

Hi,  
  
Thanks for the PR, tests are passing. However I have some comments. Would you please take the time to address those?  
  
Thanks

---

## Comment 5

> Username: raffienficiaud  
> Created_at: 2019-10-05 10:16:40 UTC  
> Url: https://github.com/boostorg/test/pull/227#issuecomment-538637000  

Thank you for having addressed all the comments and for the quality of the work. This is currently in next and should be merged to develop pretty soon.

---

## Comment 6

> Username: raffienficiaud  
> Created_at: 2019-10-21 18:29:08 UTC  
> Url: https://github.com/boostorg/test/pull/227#issuecomment-544646130  

In master, closing. Thanks!

---
