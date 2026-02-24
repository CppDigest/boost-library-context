# #398 - Incomplete tag used for type traits [Closed]

> Username: xiangfan-ms  
> Created at: 2018-05-29 20:09:51 UTC  
> Updated at: 2018-05-30 17:03:06 UTC  
> Closed at: 2018-05-30 17:02:53 UTC  
> Url: https://github.com/boostorg/hana/issues/398  

Hi Louis,  
  
I'm Xiang from the Visual C++ compiler team. This issue is found when I am trying to build boost.hana with MSVC.  
  
According to the standard, the pre-condition of certain type traits requires its input to be complete types. For example, here is the pre-condition for std::is_convertible:  
  
    From and To shall be complete types, arrays of unknown bound, or cv void types.  
  
Clang and GCC allow incomplete types and VC will give an hard error to avoid subtle issues.  
  
Strictly speaking, a hard error is not required by the standard if the program violates pre-condition, but we believe that a hard error is better than the undefined behavior.  
  
The details can be found here: https://docs.microsoft.com/en-us/cpp/cpp-conformance-improvements-2017#pre-condition-checks-for-type-traits.  
  
Hana uses several incomplete types for boost.mpl and they are internally passed to std::is_convertible.  
I guess you can repro the issue by tweaking std::is_convertible to use sizeof(T) internally.  
  
All of these usages are under the macro 'BOOST_HANA_WORKAROUND_MSVC_IS_CONVERTIBLE_INCOMPLETE_TAG' in the repo https://github.com/xiangfan-ms/hana.

---

## Comment 1

> Username: ldionne  
> Created at: 2018-05-30 03:17:47 UTC  
> Url: https://github.com/boostorg/hana/issues/398#issuecomment-393017435  

Thanks for the report. Can you please provide a sample error output and minimal test case showing the problem? I'm asking because I think this should have been fixed before this ever became a problem, in https://github.com/boostorg/fusion/issues/170. What version of Boost.Fusion are you using in your tests?

---

## Comment 2

> Username: xiangfan-ms  
> Created at: 2018-05-30 04:33:45 UTC  
> Url: https://github.com/boostorg/hana/issues/398#issuecomment-393027407  

The workaround was added about 1.5 years ago when I first tried to build hana with MSVC.  
Looks that the issue is fixed in boost 1.67 (I was using boost 1.64 which was the one available on my dev machine, but later moved to boost 1.67 because of https://github.com/boostorg/hana/issues/403. I didn't re-validate the macro BOOST_HANA_WORKAROUND_MSVC_IS_CONVERTIBLE_INCOMPLETE_TAG after the move).

---

## Comment 3

> Username: ldionne  
> Created at: 2018-05-30 17:02:53 UTC  
> Url: https://github.com/boostorg/hana/issues/398#issuecomment-393237950  

Closing this as invalid since it's been fixed already.
