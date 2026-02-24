# #84 replace uncaught_exception with uncaught_exceptions [Closed]

> Username: DanielaE  
> Created at: 2017-12-24 11:45:46 UTC  
> Updated at: 2019-09-29 15:35:05 UTC  
> Closed at: 2019-09-29 15:35:05 UTC  
> Url: https://github.com/boostorg/serialization/pull/84  

The former is deprecated in C++17 and replaced by the latter.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: robertramey  
> Created_at: 2017-12-24 18:18:59 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-353797367  

Consider getting macros for all C++ 17 features added to boost/config.  Then make make conditions which depend on these macros.  This has turned out to be a much more permanent and scalable manner of keeping the serialization library useable on C++03,...,C++17,...   Thanks for your efforts.  I depend a lot on help from my friends to keep this  this thing relevant.

---

## Comment 2

> Username: robertramey  
> Created_at: 2018-04-30 20:52:19 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-385525108  

I've looked at this a little bit more.  I think it's a bigger issue than changing from to uncaught_exceptions.  I never really considered how exceptional conditions should be handled when detected from within a destructor.  I know it's been discussed a lot, but following these discussions makes my head hurt.  I'm thinking maybe terminate() should be used but I don't really know.  For the moment, I'm not going to mess with this.  If you want to investigate this is more detail, I would appreciate it.

---

## Comment 3

> Username: pavelkryukov  
> Created_at: 2018-09-03 13:35:52 UTC  
> Updated_at: 2018-09-03 13:50:33 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-418117462  

Just to draw your attention: GCC 8 warns about deprecating `std::uncaught_exception()` and generates errors in `-Werror` compilation mode.

---

## Review 4 [Changes requested]

> Username: jeking3  
> Created_at: 2018-11-07 21:29:48 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/serialization/pull/84#pullrequestreview-172708120  

Recommend you use the method found in Boost.Log:  
  
https://github.com/boostorg/log/blob/develop/src/unhandled_exception_count.cpp#L42  
  
This is the method that Boost.Log and (eventually) Boost.Contract are going to use.

---

## Comment 5

> Username: jeking3  
> Created_at: 2018-11-07 21:30:40 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-436784328  

Probably a good idea to move common stuff down into Boost.Config so everyone can benefit.

---

## Comment 6

> Username: DanielaE  
> Created_at: 2018-11-08 06:19:34 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-436885967  

James, I agree with moving this stuff to Boost.Config rather than duplicating it all over or implementing it in different ways. But I don't agree with making things needlessly complicated and reaching out to undocumented compiler-specifics when a simple and portable solution will do the job. Well at least until Robert has got a clue what to do with the information from `uncaught_exception/s` in the first place, lest even the number of uncaught ones.

---

## Comment 7

> Username: jeking3  
> Created_at: 2018-11-08 13:18:20 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-436990700  

I'm still weary of using `__cplusplus` because of [MSVC's behavior](https://developercommunity.visualstudio.com/content/problem/139261/msvc-incorrectly-defines-cplusplus.html) there.  That's why I believe this area needs more investigation and a better implementation.

---

## Comment 8

> Username: DanielaE  
> Created_at: 2018-11-08 13:22:56 UTC  
> Updated_at: 2018-11-08 13:28:14 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-436991929  

Therefore the check for MSVC_LANG.  
It works, we're a MSVC-only company here, using a wide variety of MSVC versions and modes.  
`std::uncaught_exceptions` is a C++14 feature, and this is exactly what is checked: either reported through __cplusplus (compilers other than MSVC or latest MSVC with proper __cplusplus reporting) or MSVC_LANG (MSVC compilers without proper __cplusplus reporting)

---

## Comment 9

> Username: jeking3  
> Created_at: 2018-11-08 13:42:46 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-436997648  

Very well, however we should still move this to Boost.Config and have all three implementations use it.  Nevertheless, this could be done after.

---

## Review 10 [Approved]

> Username: jeking3  
> Created_at: 2018-11-08 13:44:37 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/serialization/pull/84#pullrequestreview-172961278  

Should factor this out to Boost.Config, but it resolves the issue here.

---

## Comment 11

> Username: jeking3  
> Created_at: 2018-11-09 01:31:27 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-437218371  

@pdimov any thoughts on a good Boost.Config macro name for this?  `BOOST_STD_UNCAUGHT_EXCEPTIONS` perhaps?

---

## Comment 12

> Username: pdimov  
> Created_at: 2018-11-09 01:35:56 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-437219216  

The convention would call for `BOOST_NO_CXX14_UNCAUGHT_EXCEPTIONS`. (https://www.boost.org/doc/libs/1_68_0/libs/config/doc/html/boost_config/boost_macro_reference.html#boost_config.boost_macro_reference.macros_that_describe_c__14_features_not_supported)

---

## Comment 13

> Username: pdimov  
> Created_at: 2018-11-09 01:37:39 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-437219519  

Or rather, `BOOST_NO_CXX17_UNCAUGHT_EXCEPTIONS`, as this is in C++17, not in C++14.

---

## Comment 14

> Username: jeking3  
> Created_at: 2018-11-09 01:45:58 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-437221103  

So you wouldn't recommend defining a macro that can be used as `BOOST_STD_UNCAUGHT_EXCEPTIONS()` but instead forcing the implementer to have an #if #else #endif in every case?

---

## Comment 15

> Username: pdimov  
> Created_at: 2018-11-09 01:54:48 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-437222713  

It's not possible to have `BOOST_STD_UNCAUGHT_EXCEPTIONS()`. You could have `BOOST_STD_UNCAUGHT_EXCEPTION()` (without the S) because the old function is implementable in terms of the new one, but not vice versa.  
  
But using such a macro kind of misses the point; the new function was added because the old function doesn't quite work. The correct use is to record the number of active exceptions in the constructor, and then check in the destructor if the new number of active exceptions is higher.  
  
It works as a minimal fix for old code as in this example, but it shouldn't be what new code would use.

---

## Comment 16

> Username: pdimov  
> Created_at: 2018-11-09 02:02:23 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-437223977  

Or, reading back this thread, perhaps @Lastique could move his implementation of `uncaught_exceptions` from Log to Core?

---

## Comment 17

> Username: Lastique  
> Created_at: 2018-11-09 09:31:35 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-437302524  

I'm ok with moving it for public use, but maybe Boost.Exception is a better place? I would cc Emil here, but I don't know his username.

---

## Comment 18

> Username: Lastique  
> Created_at: 2018-11-09 09:37:21 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-437304037  

@zajo What do you think if we move `std::uncaught_exceptions` implementation from Boost.Log to Boost.Exception? See https://github.com/boostorg/log/blob/develop/src/unhandled_exception_count.cpp.

---

## Comment 19

> Username: pdimov  
> Created_at: 2018-11-09 13:30:30 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-437359821  

No, Core is better.

---

## Comment 20

> Username: Lastique  
> Created_at: 2018-11-09 14:04:29 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-437368750  

@pdimov Why?

---

## Comment 21

> Username: pdimov  
> Created_at: 2018-11-09 14:07:46 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-437369636  

That's where we put standard functions such as f.ex. `std::quick_exit` that are missing in earlier standards. Arguably we should have a dedicated library for that but we don't.

---

## Comment 22

> Username: pdimov  
> Created_at: 2018-11-09 14:09:06 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-437369988  

(`std::exchange` too.)

---

## Comment 23

> Username: Lastique  
> Created_at: 2018-11-09 14:38:19 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-437378321  

Yes, but Boost.Exception also implements `exception_ptr`, `current_exception`, `rethrow_exception` and ultimately a library about exceptions.

---

## Comment 24

> Username: robertramey  
> Created_at: 2018-11-09 16:23:22 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-437412609  

FYI - not all libraries use Boost.Exception  
making this change addresses some change in the core language/libraries.  It's not really a feature of the the exception library.  So it should be somewhere which addresses the underlying assumptions.  possibilities: config, core, ? or something like that.

---

## Comment 25

> Username: Lastique  
> Created_at: 2018-11-10 14:52:06 UTC  
> Updated_at: 2018-11-10 14:52:23 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-437589143  

Ok, I've extracted the function as `uncaught_exceptions` to Boost.Core in https://github.com/boostorg/core/commit/82957de97029e065af787ddc9f4a21e786dfec4a.

---

## Comment 26

> Username: robertramey  
> Created_at: 2018-11-17 19:39:36 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-439642240  

So what's the final verdict?  Can I just brainlessly merge this in?  Or am In in for another death march?

---

## Comment 27

> Username: Lastique  
> Created_at: 2018-11-17 19:54:13 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-439643240  

My suggestion would be to use `boost::uncaught_exceptions` unconditionally.

---

## Comment 28

> Username: robertramey  
> Created_at: 2018-11-17 20:25:54 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-439645243  

wouldn't that make serialization require C++11 rather than C++03?

---

## Comment 29

> Username: Lastique  
> Created_at: 2018-11-17 23:32:22 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-439655490  

`boost::uncaught_exceptions` does not require C++11.

---

## Comment 30

> Username: Lastique  
> Created_at: 2019-04-13 16:33:03 UTC  
> Url: https://github.com/boostorg/serialization/pull/84#issuecomment-482833576  

This can be closed because https://github.com/boostorg/serialization/pull/140 was merged.

---
