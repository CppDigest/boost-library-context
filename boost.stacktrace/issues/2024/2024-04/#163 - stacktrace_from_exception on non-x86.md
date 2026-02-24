# #163 - stacktrace_from_exception on non-x86 [Open]

> Username: hainest  
> Created at: 2024-04-29 21:47:07 UTC  
> Updated at: 2025-11-25 02:17:09 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/163  

I submitted the below to the mailing list, but I wanted to re-iterate it here for more eyes. https://lists.boost.org/Archives/boost/2024/04/256604.php  
  
This feature is guarded by BOOST_STACKTRACE_ALWAYS_STORE_IN_PADDING, but that is only enabled on [x86](https://github.com/boostorg/stacktrace/blob/a33e198cf660c213e700d6f050a4641b74ea880a/src/exception_headers.h#L9-L13).  
This means that  
  
```console  
./bootstrap.sh --with-toolset=gcc --with-libraries=stacktrace  
./b2 install threading=multi toolset=gcc  
```  
  
always fails to build on non-x86 platforms. There are three current workarounds:  
  
1. set boost.stacktrace.from_exception=off  
2. set BOOST_STACKTRACE_LIBCXX_RUNTIME_MAY_CAUSE_MEMORY_LEAK  
3. build with threading=single  
  
(1) prevents me from using this feature. (2) shouldn't be needed on it's face because I'm using gcc/libstdc++. (3) affects more than stacktrace.  
  
Is this an incidental error, or is there a reason I wasn't able to deduce as to why this feature can't be used on non-x86+libstdc++?  
  
It seems like the check for libc++ on non-x86 can't be entirely done at compile time. I definitely understand wanting to alert users to the dangers of using this feature, but there also doesn't seem to be a good way of detecting the presence of libc++ at compile time (hence the need for [is_libcpp_runtime](https://github.com/boostorg/stacktrace/blob/a33e198cf660c213e700d6f050a4641b74ea880a/src/from_exception.cpp#L81)).

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-05-27 08:43:41 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/163#issuecomment-2132971622  

> (2) shouldn't be needed on it's face because I'm using gcc/libstdc++  
  
There are many people who use other runtimes and there's no known to me way to detect that. How should I warn them to avoid some runtimes or to be ready for leaks?

---

## Comment 2

> Username: hainest  
> Created at: 2024-05-27 23:01:56 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/163#issuecomment-2134124185  

@apolukhin I did some exploring on this some time ago for detecting minimum versions of lib{std}c++ using `_LIBCPP_GLIBC_PREREQ` to detect libc++.  
  
```  
#include <features.h>  
  
int main() {  
  
// Starting with libc++-6, the glibc macro is in the libcpp namespace  
#if defined _LIBCPP_GLIBC_PREREQ  
# if _LIBCPP_GLIBC_PREREQ(LIBC_MAJOR, LIBC_MINOR)  
    return 1;  
# endif  
#endif  
  
}  
```  
  
It was succesful for gc/clang on Ubuntu, but I didn't ever get around to doing a more thorough anaysis. The comment on namespacing referes to https://reviews.llvm.org/D41892, so this method is not 100% accurate.  
  
It looks like FreeBSD has been using libc++ since version 10 (https://wiki.freebsd.org/NewC%2B%2BStack) and OpenBSD appears to support both via clang (https://man.openbsd.org/intro.3). I'm assuming Mac uses libc++, as well. I know nothing about other platforms.

---

## Comment 3

> Username: apolukhin  
> Created at: 2024-09-12 08:24:44 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/163#issuecomment-2345590448  

I have no access to those platforms. If you have access - please implement the required fix and send a PR.

---

## Comment 4

> Username: tobim  
> Created at: 2024-11-24 07:47:34 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/163#issuecomment-2495857604  

Running into this as well now.  
  
> It seems like the check for libc++ on non-x86 can't be entirely done at compile time.  
  
I wonder why that is? Doesn't `#ifdef _LIBCPP_VERSION` do exactly that?

---

## Comment 5

> Username: brad0  
> Created at: 2024-12-17 01:36:46 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/163#issuecomment-2547314027  

> It looks like FreeBSD has been using libc++ since version 10 (https://wiki.freebsd.org/NewC%2B%2BStack) and OpenBSD appears to support both via clang (https://man.openbsd.org/intro.3). I'm assuming Mac uses libc++, as well. I know nothing about other platforms.  
  
OpenBSD uses libc++ like FreeBSD.

---

## Comment 6

> Username: wayneph01  
> Created at: 2025-08-07 18:04:11 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/163#issuecomment-3165218119  

Just found out I couldn't use `stacktrace_from_exception` on an armv7 device.  **Currently compiling with gcc and libstdc++ on x86_64 and armv7.  Using conan/cmake to define the build.**  
  
For configurations where the memory leak may be possible, but it would work, it would be nice to have an option to "opt in" to the risk.  In my case, the potential for a small leak would be preferable to having to now go look for an alternative since I already have boost in my application.  
  
Using conan, I could either put something in my armv7 profile(s) or specify the option in the self.requires(...) configuration for boost.

---

## Comment 7

> Username: Romain-Geissler-1A  
> Created at: 2025-11-25 02:17:09 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/163#issuecomment-3573516304  

> Running into this as well now.  
>   
> > It seems like the check for libc++ on non-x86 can't be entirely done at compile time.  
>   
> I wonder why that is? Doesn't `#ifdef _LIBCPP_VERSION` do exactly that?  
  
I also don't get why the specific standard library checks can't be done at compile time. From what I get in the current code, only libstdc++ and libc++ are taken into account right now. Today, by including `boost/config.hpp`, it's already possible to guard code only for libstdc++ by checking the macro `BOOST_GNU_STDLIB` (defined here: https://github.com/boostorg/config/blob/845fd033c33a7eb30e91e6f352000ef84a04808b/include/boost/config/stdlib/libstdcpp3.hpp#L12C9-L12C25). And in this Boost.Config library, indeed libc++ is detected by the presence of `_LIBCPP_VERSION` (see here: https://github.com/boostorg/config/blob/845fd033c33a7eb30e91e6f352000ef84a04808b/include/boost/config/detail/select_stdlib_config.hpp#L66). One could easily submit a change in Boost.Config to defined some new `BOOST_LLVM_STDLIB` or any similar name to detect specifically libc++.  
  
From what I understood, ideally BOOST_STACKTRACE_LIBCXX_RUNTIME_MAY_CAUSE_MEMORY_LEAK should always be defined when using libstdc++ where we know the implementation is safe, no matter which architecture is used. What I don't know is how to detect libstc++ from the jam file or the cmake file, where currently this lib is guarded to be built only on x86.
