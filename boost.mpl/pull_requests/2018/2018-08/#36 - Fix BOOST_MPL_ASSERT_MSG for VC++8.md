# #36 Fix BOOST_MPL_ASSERT_MSG for VC++8 [Merged]

> Username: robinlinden  
> Created at: 2018-08-13 18:19:34 UTC  
> Updated at: 2018-11-14 23:28:11 UTC  
> Merged at: 2018-08-23 21:40:47 UTC  
> Closed at: 2018-08-23 21:40:47 UTC  
> Url: https://github.com/boostorg/mpl/pull/36  

The struct member function inside BOOST_MPL_ASSERT_MSG_IMPL was causing  
multiple definitions of the same symbol when used inside e.g. template  
functions.  
  
An example of where this is a big issue is in the boost/geometry library  
where you currently only can call each function once for each geometry  
or you get hit by the multiple definitions linking error.  
  
This fix* works around the issue by gracefully degrading to  
BOOST_STATIC_ASSERT_MSG instead of failing during linking.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-08-13 20:06:48 UTC  
> Updated_at: 2018-08-20 18:05:56 UTC  
> Url: https://github.com/boostorg/mpl/pull/36#issuecomment-412646130  

# [Codecov](https://codecov.io/gh/boostorg/mpl/pull/36?src=pr&el=h1) Report  
> Merging [#36](https://codecov.io/gh/boostorg/mpl/pull/36?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/mpl/commit/270f33f67998ffaac6afdae25fe06be2ee744d6e?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/mpl/pull/36/graphs/tree.svg?width=650&src=pr&token=31bBblRchG&height=150)](https://codecov.io/gh/boostorg/mpl/pull/36?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #36   +/-   ##  
========================================  
  Coverage    73.33%   73.33%             
========================================  
  Files            5        5             
  Lines           15       15             
  Branches         3        3             
========================================  
  Hits            11       11             
  Misses           1        1             
  Partials         3        3  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/mpl/pull/36?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/mpl/pull/36?src=pr&el=footer). Last update [270f33f...e44b2ae](https://codecov.io/gh/boostorg/mpl/pull/36?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: eldiener  
> Created_at: 2018-08-13 20:09:09 UTC  
> Url: https://github.com/boostorg/mpl/pull/36#issuecomment-412646758  

Please provide a test which shows your fix working properly, and add that to the tests for MPL.

---

## Comment 3

> Username: robinlinden  
> Created_at: 2018-08-13 20:51:08 UTC  
> Updated_at: 2018-08-13 20:51:30 UTC  
> Url: https://github.com/boostorg/mpl/pull/36#issuecomment-412659606  

A test that would trigger the issue would involve compiling 2 files with asserts on the same class and trying to link them. I'm not sure how I'd go about doing that with the Jamfiles and everything.  
  
The way we hit it at work was by using the boost::geometry classes and calling any of [these](https://www.boost.org/doc/libs/1_68_0/libs/geometry/doc/html/geometry/reference/algorithms.html) on the same classes twice in different translation units.  
  
Is someone available to talk on IRC or anywhere else that's more convenient?

---

## Comment 4

> Username: swatanabe  
> Created_at: 2018-08-13 20:55:02 UTC  
> Url: https://github.com/boostorg/mpl/pull/36#issuecomment-412660785  

AMDG  
  
On 08/13/2018 02:51 PM, Robin Lindén wrote:  
> A test that would trigger the issue would involve compiling 2 files with asserts on the same class and trying to link them. I'm not sure how I'd go about doing that with the Jamfiles and everything.  
>   
  
link a.cpp b.cpp ;  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: eldiener  
> Created_at: 2018-08-13 20:55:23 UTC  
> Url: https://github.com/boostorg/mpl/pull/36#issuecomment-412660899  

I just want to show that your fix works in an MPL test. The test can be just a compile as long as it shows the BOOST_MPL_ASSERT_MSG macro working correctly even when used under VC++8.

---

## Comment 6

> Username: robinlinden  
> Created_at: 2018-08-13 20:58:47 UTC  
> Updated_at: 2018-08-13 21:24:39 UTC  
> Url: https://github.com/boostorg/mpl/pull/36#issuecomment-412661961  

Oh, right. Yeah, I can do that. :) Thank you!  
  
Edit: I can't find any contribution documentation. Do you want me to add a test to the existing test/assert.cpp file? ~~Because [this](https://github.com/boostorg/mpl/blob/270f33f67998ffaac6afdae25fe06be2ee744d6e/test/assert.cpp#L67) should trigger the bug under VC++8 if 2 instances of the struct it's in were to be used in different files and linked together.~~ Or maybe not. Thinking about it, I think every time we had issues it was from a template function calling boost::geometry functions. I'll look into it more tomorrow.

---

## Comment 7

> Username: eldiener  
> Created_at: 2018-08-16 21:42:54 UTC  
> Url: https://github.com/boostorg/mpl/pull/36#issuecomment-413693821  

I appreciate your test, but it should not try to specify a compiler. In other words it should compile without errors with vc++8 and with any other compiler. So please remove the:  
  
`#define BOOST_MSVC 1400`  
  
line and I will merge your change.

---

## Comment 8

> Username: robinlinden  
> Created_at: 2018-08-16 21:45:59 UTC  
> Url: https://github.com/boostorg/mpl/pull/36#issuecomment-413694477  

I can do that, but I'm not sure what my test adds over all the `BOOST_MPL_ASSERT_MSG` tests already in `test/assert.cpp`.

---

## Comment 9

> Username: robinlinden  
> Created_at: 2018-08-16 21:48:12 UTC  
> Url: https://github.com/boostorg/mpl/pull/36#issuecomment-413694989  

Want me to go ahead and squash all commits before it getting merged?

---

## Comment 10

> Username: swatanabe  
> Created_at: 2018-08-16 21:56:45 UTC  
> Url: https://github.com/boostorg/mpl/pull/36#issuecomment-413696938  

AMDG  
  
On 08/16/2018 03:46 PM, Robin Lindén wrote:  
> I can do that, but I'm not sure what my test adds over all the `BOOST_MPL_ASSERT_MSG` tests already in `test/assert.cpp`.  
>   
  
  It doesn't add anything.  What is needed is a test  
case that *fails* with the current code on VC8.  The  
current tests and your new test all pass.  
  
In Christ,  
Steven Watanabe

---

## Comment 11

> Username: robinlinden  
> Created_at: 2018-08-16 22:34:12 UTC  
> Url: https://github.com/boostorg/mpl/pull/36#issuecomment-413704647  

@swatanabe This test should show it, but I won't be 100% sure until I can test it at work tomorrow.

---

## Comment 12

> Username: eldiener  
> Created_at: 2018-08-16 23:40:28 UTC  
> Url: https://github.com/boostorg/mpl/pull/36#issuecomment-413715977  

Currently when I run the MPL tests with msvc-8.0 as my toolset the tests complete without any error. So as Steven said we need a test which would ordinarily fail, when using msvc-8.0, without your PR but which succeeds with your PR. The test must be valid for any compiler and can be just a compile test, so the test itself should not try to target vc++8.0 specifically but it should show that your PR is correct in fixing the issue.

---

## Comment 13

> Username: robinlinden  
> Created_at: 2018-08-20 17:23:39 UTC  
> Url: https://github.com/boostorg/mpl/pull/36#issuecomment-414396333  

@eldiener I had a chance to test the test at work today and it fails to link without the patch and succeeds with it.

---

## Comment 14

> Username: jeking3  
> Created_at: 2018-08-21 00:23:07 UTC  
> Url: https://github.com/boostorg/mpl/pull/36#issuecomment-414506989  

This seems to be a reasonable amount of work to cover an ancient compiler.  We run CI on VS2010 and above, noting that Visual Studio 2010 is supported through July 2020 ( https://support.microsoft.com/en-us/lifecycle/search?alpha=Visual%20Studio%202010%20(all%20editions) ) however if you look at Visual Studio 2008 and earlier (I think that's msvc-9.0, correct) it is no longer supported by Microsoft ( https://support.microsoft.com/en-us/lifecycle/search?alpha=Visual%20Studio%202008%20(all%20editions) ).  So I am inclined to say this is enough work done to verify the issue.  We should not spend any more energy supporting ancient and unsupported compilers.

---

## Comment 15

> Username: Lastique  
> Created_at: 2018-11-14 17:04:57 UTC  
> Url: https://github.com/boostorg/mpl/pull/36#issuecomment-438739090  

Just a heads up that this change had to be reverted as it caused breakage in Boost.Parameter and MSVC 8.

---

## Comment 16

> Username: jeking3  
> Created_at: 2018-11-14 19:34:13 UTC  
> Url: https://github.com/boostorg/mpl/pull/36#issuecomment-438789790  

As an aside, is that Visual Studio 2008 or earlier?  If so, it is beyond EOL.  We need to start thinking about dropping support for everything before msvc-10.0.  
  
https://support.microsoft.com/en-us/lifecycle/search?alpha=Visual%20Studio%202008%20(all%20editions)

---

## Comment 17

> Username: eldiener  
> Created_at: 2018-11-14 23:28:10 UTC  
> Url: https://github.com/boostorg/mpl/pull/36#issuecomment-438857735  

This fix does not work because BOOST_STATIC_ASSERT_MSG does not work in template code when the condition depends on template arguments, because the condition is immediately tested, whereas BOOST_MPL_ASSERT_MSG always does work in template code because it tests lazily upon instantiation of the template. Therefore the fix, as pointed out above, does not work. It was my error for initially merging the fix without realizing this, and the test provided for the fix was inadequate as it does not test within a template when the condition depends on template arguments. I do not know of another technique for testing lazily which would not incur problems with vc++8.0, but as pointed out above vc++8.0 is beyond EOL so supporting it in Boost, even in MPL, is problematical.

---
