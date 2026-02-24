# #630 Remove deprecated OS from GHA [Closed]

> Username: mborland  
> Created at: 2021-05-15 16:41:13 UTC  
> Updated at: 2021-05-18 16:08:07 UTC  
> Closed at: 2021-05-18 16:08:00 UTC  
> Url: https://github.com/boostorg/math/pull/630  

Ubuntu Xenial has been deprecated by GHA (see https://github.com/actions/virtual-environments/issues/3287). Retains the runs of GCC5, GCC6, Clang5, and Clang6.

---

## Comment 1

> Username: mborland  
> Created_at: 2021-05-17 15:45:53 UTC  
> Url: https://github.com/boostorg/math/pull/630#issuecomment-842431916  

@jzmaddock I think this is good to go. Lots of errors but they are all related to clang-9 or internal problems.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-05-17 17:53:26 UTC  
> Url: https://github.com/boostorg/math/pull/630#issuecomment-842517923  

Matt if it's OK with you, I'll merge this to the threading test clang-9 PR, and we'll try and get CI clean on there.

---

## Comment 3

> Username: mborland  
> Created_at: 2021-05-17 17:58:21 UTC  
> Url: https://github.com/boostorg/math/pull/630#issuecomment-842520863  

> Matt if it's OK with you, I'll merge this to the threading test clang-9 PR, and we'll try and get CI clean on there.  
  
Makes sense to me.

---

## Comment 4

> Username: mborland  
> Created_at: 2021-05-18 16:08:00 UTC  
> Url: https://github.com/boostorg/math/pull/630#issuecomment-843309250  

Merged into #631

---
