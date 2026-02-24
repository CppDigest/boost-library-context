# #689 Deprecate C++03 in Boost 1.73 as preparation for C+14 switch [Merged]

> Username: mloskot  
> Created at: 2020-03-27 11:17:25 UTC  
> Updated at: 2020-04-21 18:59:32 UTC  
> Merged at: 2020-03-28 23:19:59 UTC  
> Closed at: 2020-03-28 23:19:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/689  

Following discussion in #590, we are identifying support for C++03  
as a candidate for removal from future releases of Boost.Geometry.  
  
Issue [deprecation](http://eel.is/c++draft/depr#2) warning during compilation in C++03 conformance mode  
Users can define BOOST_GEOMETRY_DISABLE_DEPRECATED_03_WARNING to disable it.  
  
### Tasklist  
  
- [x] Decide between switch to C++11 or to C++14 - C++14 it is   
- [x] Decide which Boost version, 1.74 or 1.75 or later, will require the later C++ version - 1.75 it is

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2020-03-28 22:35:07 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/689#pullrequestreview-383355520  

Thank you

---

## Comment 2

> Username: Mike-Devel  
> Created_at: 2020-04-14 15:51:44 UTC  
> Updated_at: 2020-04-14 15:51:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/689#issuecomment-613523835  

@mloskot : The checks don't reflect the message: You are checking for c++11 stuff, but state that c++14 will be required.   
  
wouldnt something like `#if (__cplusplus < 201402L) && (_MSVC_LANG < 201402L) `be more appropriate?

---

## Comment 3

> Username: mloskot  
> Created_at: 2020-04-14 16:33:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/689#issuecomment-613547515  

@Mike-Devel Yes, you are correct. Initially, before we settled at C++14, we only needed to distinguish between C++03 and later.  
  
Why not  Boost macros? For example, `BOOST_NO_CXX14_CONSTEXPR`,, `BOOST_NO_CXX14_DECLTYPE_AUTO`, `BOOST_NO_CXX14_RETURN_TYPE_DEDUCTION`

---

## Comment 4

> Username: Mike-Devel  
> Created_at: 2020-04-14 16:36:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/689#issuecomment-613548990  

> Why not Boost macros?   
  
Because I didn't know them offhand ;).   
  
On the other hand  - unless you already know exactly what features you want to use - doesn't it make more sense to simply test for the c++ language version instead for individual features?

---

## Comment 5

> Username: awulkiew  
> Created_at: 2020-04-14 21:42:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/689#issuecomment-613696033  

I'm leaning towards using Boost.Config macros. Even checking single `BOOST_NO_CXX14_CONSTEXPR` would probably be enough because this is one of the features that was added last to MSVC AFAIR.  
  
The test based on `__cplusplus` and `_MSVC_LANG` would require more care and testing to make it right for all supported compilers/preprocessors. I'd assume that some compilers could complain that `_MSVC_LANG` is not defined. At least on MSVC `__cplusplus` is meaningless so it wouldn't surprise me that there is a different compiler which doesn't define `_MSVC_LANG` and also meaningless `__cplusplus`. Not to mention that earlier versions of MSVC doesn't define `_MSVC_LANG`. I also don't know what will be the values with Clang on Windows which partially emulates MSVC. Etc. Etc.  
  
On the other hand we can assume that Boost.Config works.  
  
Furthermore, this is not the only way of informing the users about the transition. In addition to this annoying macro we will also announce on the mailing lists, in the docs, release notes, slack channels, gitter, etc.

---

## Comment 6

> Username: mloskot  
> Created_at: 2020-04-14 21:49:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/689#issuecomment-613698752  

I agree. I also prefer the Boost Config macros. Moreover, even if `_MSVC_LANG` is defined regardlessoof CL.EXE switches, `_MSVC_LANG < 201402` is never true.  
  
It's good to provoke comments even on  seemingly trivial issues :)

---

## Comment 7

> Username: mloskot  
> Created_at: 2020-04-14 22:40:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/689#issuecomment-613716135  

@awulkiew I updated #696 to check for `BOOST_NO_CXX14_CONSTEXPR`. That should be enough approx. for the purpose of the deprecation warning.

---

## Comment 8

> Username: Mike-Devel  
> Created_at: 2020-04-14 23:30:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/689#issuecomment-613730961  

> Moreover, even if _MSVC_LANG is defined regardlessoof CL.EXE switches, _MSVC_LANG < 201402 is never true.  
  
That is true, but the point is that it directly expresses what you say you want to check for (c++14 support) and older versions of msvc that don't support c++14 don't define that macro, so the expression evaluates to true and you get the desired warning .  
  
Again, if you know what you want from c++14, then checking for features is a good approach,  but checking for seemingly arbitrary macros that you expect are (not) defined on compilers you plan to support seems backwards to me.  
  
Not that I have any stake in this - I don't use boost geometry.

---

## Comment 9

> Username: mloskot  
> Created_at: 2020-04-14 23:57:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/689#issuecomment-613738595  

Although I understand your point, it's valid, for this particular case of deprecation warning, any missing C++14 feature will serve fine.  
  
Once migration starts, finer feature-wise control will be needed. Then, it may also turn out that VS 2015 with incomplete C++14 support, which AFAIK does not define the the lang macro, may work fine  compiling the library.

---

## Comment 10

> Username: Mike-Devel  
> Created_at: 2020-04-15 08:02:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/689#issuecomment-613883324  

It is definitely defined in VS2015 Update 3 (probably not RTM).   
  
But it really doesn't matter. Sorry for arguing about it.

---

## Comment 11

> Username: mloskot  
> Created_at: 2020-04-15 08:33:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/689#issuecomment-613899676  

@Mike-Devel You pointed out a valid issue, so the arguing is not all that pointless :)

---

## Comment 12

> Username: Mike-Devel  
> Created_at: 2020-04-15 09:09:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/689#issuecomment-613917779  

I meant arguing about checking `__cplusplus`/`_MSVC_LANG` vs `BOOST_NO_CXX14_CONSTEXPR` ;)

---

## Comment 13

> Username: mloskot  
> Created_at: 2020-04-15 09:10:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/689#issuecomment-613918165  

Yes, I know what you mean :)  It still provoked some extra considerations which is good.

---

## Comment 14

> Username: Mike-Devel  
> Created_at: 2020-04-21 08:54:33 UTC  
> Updated_at: 2020-04-21 08:55:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/689#issuecomment-617047839  

@awulkiew , @mloskot, @barendgehrels   
   
> Furthermore, this is not the only way of informing the users about the transition. In addition to this annoying macro we will also announce on the mailing lists, in the docs, release notes, slack channels, gitter, etc.  
  
Speaking of which, I don't see release notes for boost geometry (and boost math for that matter) at https://github.com/boostorg/website/blob/master/feed/history/boost_1_73_0.qbk

---

## Comment 15

> Username: mloskot  
> Created_at: 2020-04-21 15:54:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/689#issuecomment-617258639  

@Mike-Devel I've just submitted the release notes https://github.com/boostorg/website/pull/499

---

## Comment 16

> Username: mloskot  
> Created_at: 2020-04-21 18:45:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/689#issuecomment-617345609  

@awulkiew I also added in https://github.com/boostorg/geometry/commit/56a9f79036dc3bce8dcd0483cfa728a196997f81 the deprecation notice to `doc/release_notes.qbk`. It may be too late to merge the develop to master though.

---

## Comment 17

> Username: awulkiew  
> Created_at: 2020-04-21 18:59:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/689#issuecomment-617352590  

@mloskot Master is still opened so it's not too late. I'll do it today.

---
