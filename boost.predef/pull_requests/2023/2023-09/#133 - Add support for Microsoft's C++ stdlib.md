# #133 Add support for Microsoft's C++ stdlib [Merged]

> Username: BurningEnlightenment  
> Created at: 2023-09-20 07:31:32 UTC  
> Updated at: 2023-10-31 12:01:23 UTC  
> Merged at: 2023-10-31 12:01:23 UTC  
> Closed at: 2023-10-31 12:01:23 UTC  
> Url: https://github.com/boostorg/predef/pull/133  

Explicit support for detecting Microsoft's C++ stdlib is necessary due to the fact that the Dinkumware version-number has been frozen years ago and that this stdlib can be used in conjunction with Clang, i.e relying on `BOOST_COMP_MSVC` to workaround stdlib bugs does not work in all cases.  
  
This patch should work as is, however, ~I'm unsure what to do with `_MSVC_STL_UPDATE` as it exceeds the digit space allocated for the patchlevel (5 < 6). So I ignored it for now.~ After thinking a bit about this I believe that it should be fine to encode it as `_MSVC_STL_UPDATE % 100000L` i.e. the default behaviour of `BOOST_VERSION_NUMBER`. There are no `_MSVC_STL_UPDATE ` values preceding 2015, i.e. this encoding wouldn't mess with version ordering. As an added benefit `BOOST_VERSION_NUMBER(14, 3, 202302L)` would just work:tm: and do what people would expect.  
  
Resolves #132

---

## Comment 1

> Username: BurningEnlightenment  
> Created_at: 2023-09-20 08:18:27 UTC  
> Url: https://github.com/boostorg/predef/pull/133#issuecomment-1727208051  

I'm sorry for the force pushes and wasted CI time. VSCode's go-to managed to open a `predef/make.h` from a completely different boost installation which is why the commit initially didn't contain `BOOST_PREDEF_MAKE_10_VVR` which in turn caused the CI to fail with an error about unexpected tokens before newline which I initially misinterpreted as a CRLF issue 😣

---

## Review 2 [Changes requested]

> Username: grafikrobot  
> Created_at: 2023-10-15 14:27:46 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/predef/pull/133#pullrequestreview-1678803787  

Thank you for the contribution. And sorry it took a bit to review. First the easy things to change: it would better to have the header name match the def. I.e. visualc.h ==> msvc.h, need to add tests for the new MAKE_10_VVR macro in test/make.cpp.  
  
Second, the hard ones: the _MSVC_STL_VERSION def is not enough to distinguish releases of the MS STL. For example here are the defs I have for two versions of msvc:  
  
-- 14.37.32822 --  
```  
#define _CPPLIB_VER       650  
#define _MSVC_STL_VERSION 143  
#define _MSVC_STL_UPDATE  202305L  
```  
  
-- 14.36.32532 --  
```  
#define _CPPLIB_VER       650  
#define _MSVC_STL_VERSION 143  
#define _MSVC_STL_UPDATE  202302L  
```  
  
Hence we need to take into account the _MSVC_STL_UPDATE value for the version. The obvious thing to do would be to make that the patch number. Unfortunately it's 6 digits which is out of bounds for the current patch number encoding. One possibility would be to encode the patch number as (_MSVC_STL_UPDATE-190000L).

---

## Comment 3

> Username: BurningEnlightenment  
> Created_at: 2023-10-15 18:29:11 UTC  
> Url: https://github.com/boostorg/predef/pull/133#issuecomment-1763469204  

> Hence we need to take into account the _MSVC_STL_UPDATE value for the version. The obvious thing to do would be to make that the patch number. Unfortunately it's 6 digits which is out of bounds for the current patch number encoding. One possibility would be to encode the patch number as (_MSVC_STL_UPDATE-190000L).  
  
After thinking a bit about this I believe that it should be fine to encode it as `_MSVC_STL_UPDATE % 100000L` i.e. the default behaviour of `BOOST_VERSION_NUMBER`. There are no `_MSVC_STL_UPDATE ` values preceding 2015, i.e. this encoding wouldn't mess with version ordering. As an added benefit `BOOST_VERSION_NUMBER(14, 3, 202302L)` would just work:tm: and do what people would expect. Does that make sense?

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2023-10-17 03:46:15 UTC  
> Url: https://github.com/boostorg/predef/pull/133#issuecomment-1765617183  

The latest changes look good. But something strange is happening with the test results. For some combinations of windows + msvc there's no BOOST_LIB_STD_* detected at all. Example: <https://dev.azure.com/grafikrobot/Hash%20Predef/_build/results?buildId=1281&view=logs&j=b269197d-edfd-5389-3f7d-651c3b58525d&t=ddc29673-75b0-5756-6b17-c2cb30462705>. I suspect it's not your changes causing the problem. But not sure.

---

## Comment 5

> Username: BurningEnlightenment  
> Created_at: 2023-10-18 06:43:46 UTC  
> Url: https://github.com/boostorg/predef/pull/133#issuecomment-1767774783  

> But something strange is happening with the test results. For some combinations of windows + msvc there's no BOOST_LIB_STD_* detected at all.  
  
There is a test executable compiled as C and one compiled as C++. The former correctly forgoes C++ stdlib detection while the latter correctly detects  
```  
BOOST_LIB_STD_DINKUMWARE = 65000000 (6,50,0) | Dinkumware  
BOOST_LIB_STD_MSVC = 140302210 (14,3,2210) | Microsoft stdlib  
```  
-- ll. 169-170 of your linked log ([permalink](https://dev.azure.com/grafikrobot/Hash%20Predef/_build/results?buildId=1281&view=logs&j=b269197d-edfd-5389-3f7d-651c3b58525d&t=ddc29673-75b0-5756-6b17-c2cb30462705&l=170)).   
I suspect that test execution isn't explicitly ordered.  
  
However, I took the chance to update the header guard names and add some documentation w.r.t. `_MSVC_STL_UPDATE`.

---

## Comment 6

> Username: BurningEnlightenment  
> Created_at: 2023-10-28 09:01:20 UTC  
> Url: https://github.com/boostorg/predef/pull/133#issuecomment-1783753369  

@grafikrobot can we get this in before 1.84 is closed for major changes?

---
