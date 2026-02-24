# #98 defect macro for C++17 feature `std::invoke()` [Merged]

> Username: olk  
> Created at: 2016-10-14 18:51:22 UTC  
> Updated at: 2018-04-05 17:14:14 UTC  
> Merged at: 2016-10-20 17:09:57 UTC  
> Closed at: 2016-10-20 17:09:57 UTC  
> Url: https://github.com/boostorg/config/pull/98  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2016-10-19 18:43:12 UTC  
> Url: https://github.com/boostorg/config/pull/98#issuecomment-254904191  

My only complaint about this PR is that clang with libstdc++ pulls in <functional> unconditionally - we've worked pretty hard _not_ to pull in large std lib headers like that, is there any way around this?  Any new headers in 6.1 we can test for?

---

## Comment 2

> Username: olk  
> Created_at: 2016-10-20 06:56:29 UTC  
> Updated_at: 2016-10-20 06:56:44 UTC  
> Url: https://github.com/boostorg/config/pull/98#issuecomment-255024489  

maybe `<experimental/filesystem>` could be used

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2016-10-20 10:21:47 UTC  
> Url: https://github.com/boostorg/config/pull/98#issuecomment-255067820  

<experimental/filesystem> was present in gcc-5.3.0, however there are a number of experimental C++17 headers new in gcc-6.1.0, and <experimental/memory_resource> looks like a good candidate?

---

## Comment 4

> Username: olk  
> Created_at: 2016-10-20 10:53:10 UTC  
> Url: https://github.com/boostorg/config/pull/98#issuecomment-255073844  

hmm - I used 'GCC 6 Release Series Changes, New Features, and Fixes' (https://gcc.gnu.org/gcc-6/changes.html) : 'An experimental implementation of the File System TS.'  
  
I can't find a header usable for our purposes.  
  
Maybe we could use the support for  mathematical special functions (ISO/IEC 29124:2010), e.g. test __STDCPP_MATH_SPEC_FUNCS__for value 201003L.  
what do you think?

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2016-10-20 11:46:10 UTC  
> Url: https://github.com/boostorg/config/pull/98#issuecomment-255083356  

On 20/10/2016 11:53, Oliver Kowalke wrote:  
  
> hmm - I used 'GCC 6 Release Series Changes, New Features, and Fixes'   
> (https://gcc.gnu.org/gcc-6/changes.html) : 'An experimental   
> implementation of the File System TS.'  
>   
> I can't find a header usable for our purposes.  
  
What was wrong with <experimental/memory_resource> ?  
  
It doesn't get explicitly mentioned in the release notes, but there are   
a ton of new (and some old) headers in gcc-6.x, probably covered by   
"Experimental support for most features of the second version of the   
Library Fundamentals TS"?  
  
> Maybe we could use the support for mathematical special functions   
> (ISO/IEC 29124:2010), e.g. test __STDCPP_MATH_SPEC_FUNCS__for value   
> 201003L.  
> what do you think?  
>   
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub   
> https://github.com/boostorg/config/pull/98#issuecomment-255073844,   
> or mute the thread   
> https://github.com/notifications/unsubscribe-auth/AEx5OCvkdAThBSuwz2qeRuMEbL2CdYo_ks5q10gWgaJpZM4KXU_x.

---

## Comment 6

> Username: olk  
> Created_at: 2016-10-20 11:52:58 UTC  
> Updated_at: 2016-10-20 11:53:16 UTC  
> Url: https://github.com/boostorg/config/pull/98#issuecomment-255084662  

changed to `<experimental/memory_resource>`

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2018-04-05 09:35:53 UTC  
> Url: https://github.com/boostorg/config/pull/98#issuecomment-378877474  

We have a bit of an issue with this one that I'm not sure what to do about: the original specification of `std::invoke` was changed at the very last minute for C++17 with `result_of` becoming deprecated and replaced by `invoke_result`.  Over the last couple of days I've updated the test case for this, and fixed up the resulting failures - but there's a catch - only the latest compilers (msvc-14.1up3, gcc-7) support a std conforming `invoke`, and one (clang+libc++) not at all.  
Currently, only Boost.Context is using this macro, and I see there is a workaround in place for BOOST_NO_STD_INVOKE defined.  So my question are:  
  
* What should this macro signify? std conforming `invoke` or simply it's presence?  
* What's the impact on existing users for defining this macro in more situations?  
  
I'm asking because @NAThompson has some changes to Boost.Math which (probably) require the "fully conforming" meaning.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2018-04-05 09:56:37 UTC  
> Url: https://github.com/boostorg/config/pull/98#issuecomment-378882959  

See also: https://github.com/boostorg/config/issues/221

---

## Comment 9

> Username: olk  
> Created_at: 2018-04-05 13:52:57 UTC  
> Url: https://github.com/boostorg/config/pull/98#issuecomment-378943702  

BOOST_NO_STD_INVOKE should test for std::invoke conformance

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2018-04-05 16:54:12 UTC  
> Url: https://github.com/boostorg/config/pull/98#issuecomment-379003557  

>BOOST_NO_STD_INVOKE should test for std::invoke conformance  
  
So: just for the avoidance of doubt - does that mean you're OK with the changes?

---

## Comment 11

> Username: olk  
> Created_at: 2018-04-05 17:13:53 UTC  
> Updated_at: 2018-04-05 17:14:14 UTC  
> Url: https://github.com/boostorg/config/pull/98#issuecomment-379009512  

Yes, I've made some modifications and "Travis Build #56" passed.

---
