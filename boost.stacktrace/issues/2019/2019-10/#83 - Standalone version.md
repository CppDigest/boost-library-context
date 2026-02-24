# #83 - Standalone version [Open]

> Username: ceztko  
> Created at: 2019-10-31 00:11:31 UTC  
> Updated at: 2024-09-12 08:09:09 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/83  

Is there a standalone version od the library? If no, how easy it would be to strip it from boost just using C++14 standard libraries?

---

## Comment 1

> Username: ceztko  
> Created at: 2019-11-16 13:24:46 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/83#issuecomment-554637163  

After the sad news about the [rejection](https://github.com/cplusplus/papers/issues/119) for c++20, I think having a standalone version became more urgent. Boost aims to have many libraries inserted in the standard but many simple can't afford to add boost as a dependency or often the burden to add such big (compile time) dependency for just one package is simple not worth, for example in embedded platforms, ios/android development.

---

## Comment 2

> Username: apolukhin  
> Created at: 2019-11-19 19:35:07 UTC  
> Updated at: 2019-11-19 19:35:20 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/83#issuecomment-555673629  

It's not rejected, it just didn't get on time for C++20. C++23 is the new milestone (and only LWG review remains).  
  
If you want a standalone version - just do it :-) Fork and remove the Boost dependencies. I would provide a link in the Boost docs to the forked standalone version.

---

## Comment 3

> Username: eyalroz  
> Created at: 2020-04-02 12:55:00 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/83#issuecomment-607828560  

@apolukhin : Do you think you could make it easier for us by pointing out "hitches" in yanking out the Boost [dependencies](https://github.com/boostorg/stacktrace/search?q=%22%23include+%3Cboost%2F%22&unscoped_q=%22%23include+%3Cboost%2F%22)?

---

## Comment 4

> Username: apolukhin  
> Created at: 2020-05-05 17:06:18 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/83#issuecomment-624182290  

Most of the dependencies are in tests. It seems fine to leave tests dependent on Boost, while making the remaining parts of the library standalone.

---

## Comment 5

> Username: eyalroz  
> Created at: 2020-06-03 14:49:29 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/83#issuecomment-638248391  

Well, some dependencies are not in tests. For example, in `stacktrace.hpp` we have:  
```  
#include <boost/core/explicit_operator_bool.hpp>  
#include <boost/core/no_exceptions_support.hpp>  
#include <boost/container_hash/hash_fwd.hpp>  
```

---

## Comment 6

> Username: eyalroz  
> Created at: 2020-06-03 22:33:01 UTC  
> Updated at: 2020-07-15 08:57:22 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/83#issuecomment-638495863  

Actually, there are lots of dependencies in the library includes. I've started working on getting rid of them (and made it easy for me by assuming C++11 or later), but some of that is not trivial (e.g. replacing NOINLINE and FORCE_INLINE, `boost::demagle` etc.). Then there is the matter of switching from the Boost build system to CMake, to make life more sane - and that also means generating a configuration file using CMake checks, trying to compile the files in `build/` from within CMake, and so on.  
  
So, lots and lots of work.  
  
I'm working on this at [my fork repo](https://github.com/eyalroz/stacktrace/) - whoever is interested is welcome to go there and either submit a PR or file an issue.

---

## Comment 7

> Username: eyalroz  
> Created at: 2020-07-15 08:58:18 UTC  
> Updated at: 2020-07-15 17:23:54 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/83#issuecomment-658641272  

Update: I'm making progress in removing Boost dependencies:  
  
* Using a CMake-based build system  
* Remaining Boost dependencies: winapi; everything else is self-contained.  
* Examples build and run, except for one which crashes.  
* I'm not able to get the library to properly decrypt stack traces using the instructions in the documentation, i.e. I get opaque numbers for each stack trace line.  
  
I could really use some help from people with Windows experience.

---

## Comment 8

> Username: ceztko  
> Created at: 2020-07-15 09:06:01 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/83#issuecomment-658645737  

What kind of help you need for the Windows part?

---

## Comment 9

> Username: eyalroz  
> Created at: 2020-07-15 09:52:20 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/83#issuecomment-658670296  

@ceztko :   
  
1. Figure out how to extract the minimal relevant part of boost's winapi/ code to meet stacktrace's needs.  
2. Add dependencies and settings in `CMakeLists.txt` to:  
    2.1 Correctly choose what gets built on Windows  
    2.2 Ensure the build on Windows actually passes (or at least - relay bugs to me and correspond about addressing them on the bug page)  
3. Check the small bits of Windows-related code I've introduced (mostly `boost-modified/config/platform.hpp`) to make sure I haven't missed anything

---

## Comment 10

> Username: TomArrow  
> Created at: 2023-04-21 08:49:18 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/83#issuecomment-1517494161  

Has anyone ever managed to do this?

---

## Comment 11

> Username: eyalroz  
> Created at: 2023-04-21 09:43:49 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/83#issuecomment-1517565385  

@TomArrow :   
Well, a couple of years back, I worked on this:  
  
https://github.com/eyalroz/stacktrace  
  
and it was kinda-working. But I haven't kept it up-to-date. Also, stack traces are going into the standard in C++23, so not a lot of motivation to work on stand-alone just for older code I suppose. Still, you're welcome to try my repo.
