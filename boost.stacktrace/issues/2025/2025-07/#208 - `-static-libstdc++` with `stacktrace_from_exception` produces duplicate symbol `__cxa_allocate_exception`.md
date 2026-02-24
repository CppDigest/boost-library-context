# #208 - `-static-libstdc++` with `stacktrace_from_exception` produces duplicate symbol `__cxa_allocate_exception` [Open]

> Username: godexsoft  
> Created at: 2025-07-24 13:48:42 UTC  
> Updated at: 2026-02-06 08:49:17 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/208  

I'm trying to update our project (The XRP ledger) to Boost 1.88 and I'm seeing a linker error on x86 (where `stacktrace_from_exception` gets enabled by default):  
```  
/usr/bin/ld: /usr/bin/../lib/gcc/x86_64-linux-gnu/12/libstdc++.a(eh_alloc.o): in function `__cxa_allocate_exception':  
(.text.__cxa_allocate_exception+0x0): multiple definition of `__cxa_allocate_exception'; /root/.conan2/p/b/boost4d02c12b1f707/p/lib/libboost_stacktrace_from_exception.a(from_exception.o):/root/.conan2/p/boost9ad4e6f6d8220/s/src/libs/stacktrace/build/../src/from_exception.cpp:248: first defined here  
```  
  
This only happens with `-static-libstdc++  -static-libgcc` options.  
There seems to be no easy way to disable the component through conan. I tried something like  
```  
def configure(self):  
    self.options["boost"].extra_b2_flags = "boost.stacktrace.from_exception=off"  
```  
  
which results in  
```  
boost/1.88.0: WARN: Boost component 'stacktrace_from_exception' is missing libraries. Try building boost with '-o boost:without_stacktrace_from_exception'. (Option is not guaranteed to exist)  
ERROR: boost/1.88.0: Error in package_info() method, line 2026  
        raise ConanException(f"These libraries were expected to be built, but were not built: {non_built}")  
        ConanException: These libraries were expected to be built, but were not built: {'boost_stacktrace_from_exception'}  
```  
  
Here is the real project breaking: https://github.com/XRPLF/rippled/actions/runs/16486148700/job/46611443287?pr=5570   
Any help and suggestions are very welcome 🙏

---

## Comment 1

> Username: JeffGarland  
> Created at: 2025-08-06 15:29:06 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/208#issuecomment-3160638887  

We're also seeing this on an internal project with gcc15 targeting Linux.

---

## Comment 2

> Username: godexsoft  
> Created at: 2025-08-06 16:04:17 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/208#issuecomment-3160753971  

@JeffGarland in our case this solved the issue: https://github.com/XRPLF/rippled/pull/5264#issuecomment-3113018978  
This is not a generic solution but maybe it will help you figure out how you can workaround 👍

---

## Comment 3

> Username: JeffGarland  
> Created at: 2025-08-10 21:39:36 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/208#issuecomment-3172910743  

@godexsoft Thanks for the pointer. We were also able to work around the issue since our dependency on boost.stacktrace was 'accidental' -- that is we had a stray header include despite deprecating using some time ago.  I did try to look at the source to see if I could figure out why this has become an issue, but ran out of time.  Seems clearly like a macro mismatch and boost shouldn't be attempting to define those symbols.

---

## Comment 4

> Username: uilianries  
> Created at: 2025-09-04 09:06:20 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/208#issuecomment-3252621153  

Hello! This case seems more related to the Conan recipe for Boost than this library. I would suggest moving this issue to https://github.com/conan-io/conan-center-index/issues/

---

## Comment 5

> Username: fanenr  
> Created at: 2026-02-06 08:49:17 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/208#issuecomment-3858861061  

This does not appear to be a Conan issue:  
  
My boost was manually compiled and installed via b2, and I only used boost::stacktrace in the project via CMake. This problem also occurs when I set the `-static-libstdc++` option and link `stacktrace_from_exception`.
