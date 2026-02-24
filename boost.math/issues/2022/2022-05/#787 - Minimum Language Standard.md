# #787 - Minimum Language Standard [Closed]

> Username: mborland  
> Created at: 2022-05-23 03:01:39 UTC  
> Updated at: 2022-06-01 07:06:48 UTC  
> Closed at: 2022-06-01 03:19:22 UTC  
> Url: https://github.com/boostorg/math/issues/787  

Based on recent discussion in the ML started by @jzmaddock are we in favor of increasing the minimum language standard of Math? I would be in favor of at least 14 or 17. From the [GCC Page](https://gcc.gnu.org/projects/cxx-status.html#cxx11) g++5 (the oldest we currently test on) has complete C++-14 support. For complete C++-17 support we would move to g++7. Second order effect would be reducing our CI battery which now takes over 6 hours to run on GHA.  
  
Thoughts @NAThompson and @ckormanyos

---

## Comment 1

> Username: NAThompson  
> Created at: 2022-05-23 03:20:02 UTC  
> Updated at: 2022-05-23 03:20:35 UTC  
> Url: https://github.com/boostorg/math/issues/787#issuecomment-1134129886  

I'm pro-14; primarily due to the `[deprecated]` tag. It's *so* hard to evolve the library without it.  
  
I'd say new features should be done in 17/20.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2022-05-23 07:16:51 UTC  
> Url: https://github.com/boostorg/math/issues/787#issuecomment-1134275909  

> in favor of increasing the minimum language standard of Math  
  
Although I encouraged retention of 11, I've got no issue whatsoever if the consensus is to jump to 14 or higher.  
  
> g++5 (the oldest we currently test on) has complete C++-14  
  
There will probalby be some inconsistencies in GCC5's implementation of C++14. That compiler was coming of age around 2013 so the specification was really new at that time. If GCC5 proves to be problematic, one compromise might be reducing its support to a subset of core functions such as most of spec-func.  
  
> Second order effect would be reducing our CI battery which now takes over 6 hours to run on GHA...  
  
And we keep adding even more compilers in the advent of Jammy, etc.  
  
It's not my decision, but here are some ideas I've used on other projects.  
- Consider reducing the sope of CI on GCC5.  
- Some of the compilations might be redundant. I've found, for instance, that folks tend to stick to the _endpoints_ so on GCC 11 and clang 12, for instance, maybe just run in CI `-std=c++11` and `-std=c++20`.  
- There might be similar endpoint-like redundancies around the entire lineage of compilers.

---

## Comment 3

> Username: mborland  
> Created at: 2022-05-23 15:28:50 UTC  
> Url: https://github.com/boostorg/math/issues/787#issuecomment-1134820855  

> There will probalby be some inconsistencies in GCC5's implementation of C++14. That compiler was coming of age around 2013 so the specification was really new at that time. If GCC5 proves to be problematic, one compromise might be reducing its support to a subset of core functions such as most of spec-func.  
>   
  
I've linked a PR giving C++14 a try with GCC5 so we'll see how that goes.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2022-05-23 19:57:46 UTC  
> Url: https://github.com/boostorg/math/issues/787#issuecomment-1135082412  

>> probalby be some inconsistencies in GCC5's implementation of C++14  
  
> I've linked a PR giving C++14 a try with GCC5 so we'll see how that goes  
  
Great idea! Thank you Matt

---

## Comment 5

> Username: NAThompson  
> Created at: 2022-05-23 20:39:11 UTC  
> Url: https://github.com/boostorg/math/issues/787#issuecomment-1135116778  

However, remember the primary purpose of setting the minimum language standard to C++14 is to not have to deal with inconsistent C++11.  
  
Also, the only thing I use out of C++14 is the `[[deprecated]]` tag; if the compiler doesn't get that I think we should say 'don't support'.

---

## Comment 6

> Username: mborland  
> Created at: 2022-05-24 00:58:39 UTC  
> Url: https://github.com/boostorg/math/issues/787#issuecomment-1135285492  

The CI run for the linked PR is clean so we would have no issues moving to C++14 even with GCC-5 testing.

---

## Comment 7

> Username: jzmaddock  
> Created at: 2022-05-24 17:17:08 UTC  
> Url: https://github.com/boostorg/math/issues/787#issuecomment-1136218149  

Bare in mind that we will need to give notice of the change and deprecate C++11 support for about 3 release prior to the change.  
  
if ``[[deprecated]]`` is the only stumbling block, then GCC has ``__attribute(deprecated)__`` and msvc has ``__declspec(deprecated)``, so we could probably implement that way back to C++03 if we really had to ;)

---

## Comment 8

> Username: ckormanyos  
> Created at: 2022-05-25 17:55:36 UTC  
> Updated at: 2022-05-25 17:57:21 UTC  
> Url: https://github.com/boostorg/math/issues/787#issuecomment-1137645892  

>> I would be in favor of at least 14  
  
The call for 14 seems to be loud and clear in this thread and elsewhere.  
  
>  we will need to give notice of the change and deprecate C++11 support for about 3 release prior to the change  
  
If/when we do this, I think we should synchronize the exact same change(s) in Multiprecision, as these libs are intimately linked with each other.  
  
- Why wait?  
- Is there a consensus already?  
- Or is this too quick?  
- If we want to hit, say, summer 2023, should we add mention in the docs for both Math and Multiprecision in the July trimester for 1.80 (to be switched in summer 2023)?  
  
Thoughts?  
  
@jzmaddock and @NAThompson and @mborland

---

## Comment 9

> Username: ckormanyos  
> Created at: 2022-05-28 09:36:12 UTC  
> Updated at: 2022-05-28 09:37:17 UTC  
> Url: https://github.com/boostorg/math/issues/787#issuecomment-1140222026  

> If we want to hit, say, summer 2023, should we add mention in the docs  
  
... like similar to this? I must admit, it looked pretty harsh upon seeing it.  
  
## ⚠️  Deprecation of C++11  
**Expected as of July 1, 2023, Boost.Math raises minimum standard to C++14 (and C++11 will no longer be supported)**

---

## Comment 10

> Username: mborland  
> Created at: 2022-05-28 15:30:55 UTC  
> Url: https://github.com/boostorg/math/issues/787#issuecomment-1140284041  

> > If we want to hit, say, summer 2023, should we add mention in the docs  
>   
> ... like similar to this? I must admit, it looked pretty harsh upon seeing it.  
>   
> ## ⚠️ Deprecation of C++11  
> **Expected as of July 1, 2023, Boost.Math raises minimum standard to C++14 (and C++11 will no longer be supported)**  
  
I think that works. I can edit the attached PR to add C++14 testing to GCC-5 so we know that we are not going to break anything in the meantime.

---

## Comment 11

> Username: NAThompson  
> Created at: 2022-05-30 01:16:31 UTC  
> Url: https://github.com/boostorg/math/issues/787#issuecomment-1140577775  

I guess the way to do this is to also put a `#warning` that gets emitted in C++11 mode, as I expect few people will read the README and fewer still will remember.

---

## Comment 12

> Username: ckormanyos  
> Created at: 2022-06-01 07:06:48 UTC  
> Url: https://github.com/boostorg/math/issues/787#issuecomment-1143197164  

See also https://github.com/boostorg/multiprecision/issues/467
