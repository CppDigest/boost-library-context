# #617 Add Travis CI build with latest Boost libs [Merged]

> Username: jszuppe  
> Created at: 2016-06-02 18:02:06 UTC  
> Updated at: 2016-06-04 10:00:05 UTC  
> Merged at: 2016-06-04 03:09:32 UTC  
> Closed at: 2016-06-04 03:09:32 UTC  
> Url: https://github.com/boostorg/compute/pull/617  

Two additional Travis-CI builds (gcc and clang) to be sure that Boost.Compute works with libraries from the latest Boost version. Spoiler: it works.  
  
POCL builds may not work now. It has nothing to do with this pull-request, it's just because llvm [turned off their APT mirror](http://lists.llvm.org/pipermail/llvm-foundation/2016-May/000020.html) and gpg key can't be download.

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-06-02 19:16:09 UTC  
> Url: https://github.com/boostorg/compute/pull/617#issuecomment-223393382  

[![Coverage Status](https://coveralls.io/builds/6439986/badge)](https://coveralls.io/builds/6439986)  
  
Coverage remained the same at 80.861% when pulling **c046a5f9766368653efb474c3bb0436bac5c8b42 on haahh:pr_travis_ci_boost161** into **4c31d071309900d0b2d868e298bdb6ade045dc00 on boostorg:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2016-06-04 03:09:40 UTC  
> Url: https://github.com/boostorg/compute/pull/617#issuecomment-223732833  

Looks good, merged!

---
