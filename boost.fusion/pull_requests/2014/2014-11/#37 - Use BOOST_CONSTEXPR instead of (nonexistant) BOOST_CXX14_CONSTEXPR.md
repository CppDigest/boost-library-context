# #37 Use BOOST_CONSTEXPR instead of (nonexistant) BOOST_CXX14_CONSTEXPR. [Merged]

> Username: ldionne  
> Created at: 2014-11-11 23:05:47 UTC  
> Updated at: 2014-11-12 16:24:06 UTC  
> Merged at: 2014-11-11 23:23:14 UTC  
> Closed at: 2014-11-11 23:23:14 UTC  
> Url: https://github.com/boostorg/fusion/pull/37  

All the unit tests on develop pass on Mac OS X with Apple's Clang.

---

## Comment 1

> Username: K-ballo  
> Created_at: 2014-11-11 23:26:11 UTC  
> Url: https://github.com/boostorg/fusion/pull/37#issuecomment-62640987  

Sorry but this looks just wrong, the change was done in order to fix broken code under C++11 mode. The relevant macro can be found here https://github.com/boostorg/config/blob/master/include/boost/config/suffix.hpp#L942

---

## Comment 2

> Username: djowel  
> Created_at: 2014-11-11 23:28:42 UTC  
> Url: https://github.com/boostorg/fusion/pull/37#issuecomment-62641298  

That makes sense. I was wondering why I am not getting an error. Let's see how it goes and I'll revert.

---

## Comment 3

> Username: K-ballo  
> Created_at: 2014-11-11 23:31:53 UTC  
> Url: https://github.com/boostorg/fusion/pull/37#issuecomment-62641600  

It will go badly, things like functions returning `void` cannot be `constexpr` in C++11. We already had that broken code before, it skipped checks because clang accepts it as an extension.

---

## Comment 4

> Username: djowel  
> Created_at: 2014-11-11 23:35:50 UTC  
> Url: https://github.com/boostorg/fusion/pull/37#issuecomment-62642033  

Reverted.

---

## Comment 5

> Username: djowel  
> Created_at: 2014-11-11 23:36:36 UTC  
> Url: https://github.com/boostorg/fusion/pull/37#issuecomment-62642100  

Thanks for spotting this, K-ballo.

---

## Comment 6

> Username: ldionne  
> Created_at: 2014-11-12 14:55:24 UTC  
> Url: https://github.com/boostorg/fusion/pull/37#issuecomment-62729964  

Sorry about that. It was [my submodules not being in sync](http://thread.gmane.org/gmane.comp.lib.boost.devel/255247/focus=255287).

---

## Comment 7

> Username: Flast  
> Created_at: 2014-11-12 16:24:06 UTC  
> Url: https://github.com/boostorg/fusion/pull/37#issuecomment-62745836  

@ldionne Running `git pull && git submodule update --init` will help you to sync all submodules.

---
