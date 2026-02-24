# #690 Fix building Boost.Build with a chosen toolset [Closed]

> Username: amyspark  
> Created at: 2022-09-30 13:27:56 UTC  
> Updated at: 2022-11-27 01:02:26 UTC  
> Closed at: 2022-11-27 01:02:21 UTC  
> Url: https://github.com/boostorg/boost/pull/690  

👋   
  
Hi! This PR proposes to upstream a small change that we are shipping in Krita starting with 5.1 (https://invent.kde.org/graphics/krita/-/commit/2876937bf2dfea45645c31ebb749b1a0d08d067b).  
  
In short, this will enable users of the CMD bootstrap file to build Boost with a toolset other than MSVC. We use it in particular to target the llvm-mingw compiler, through a custom user-config.jam. (It also applies to the official MSYS2 CLANG* flavours, which I use for my own personal development needs.)  
  
cc @dimula73

---

## Comment 1

> Username: mclow  
> Created_at: 2022-10-04 02:02:46 UTC  
> Url: https://github.com/boostorg/boost/pull/690#issuecomment-1266299566  

This looks very similar to #682

---

## Comment 2

> Username: amyspark  
> Created_at: 2022-10-06 16:46:07 UTC  
> Updated_at: 2022-10-06 16:57:11 UTC  
> Url: https://github.com/boostorg/boost/pull/690#issuecomment-1270396975  

That's correct @mclow. However, the other PR neither is documented nor does it specify the rationale for the proposed change, so that's why I didn't spot it before submitting this one.

---

## Comment 3

> Username: amyspark  
> Created_at: 2022-11-21 01:32:19 UTC  
> Url: https://github.com/boostorg/boost/pull/690#issuecomment-1321324925  

@mclow I see there's a window open for bugfixes, would it be possible for this to get reviewed?

---

## Comment 4

> Username: swatanabe  
> Created_at: 2022-11-25 03:01:32 UTC  
> Url: https://github.com/boostorg/boost/pull/690#issuecomment-1326972231  

#513 looks like another duplicate

---

## Comment 5

> Username: amyspark  
> Created_at: 2022-11-27 01:02:21 UTC  
> Url: https://github.com/boostorg/boost/pull/690#issuecomment-1328144276  

Ok, closing this one in favour of #513.

---
