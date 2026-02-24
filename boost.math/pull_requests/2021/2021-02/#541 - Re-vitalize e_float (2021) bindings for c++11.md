# #541 Re-vitalize e_float (2021) bindings for c++11 [Merged]

> Username: ckormanyos  
> Created at: 2021-02-15 22:00:29 UTC  
> Updated at: 2021-02-24 19:23:38 UTC  
> Merged at: 2021-02-24 19:16:13 UTC  
> Closed at: 2021-02-24 19:16:13 UTC  
> Url: https://github.com/boostorg/math/pull/541  

This draft PR exposes the re-vitalization of math bindings for `e_float` (2021) upgraded for modern Boost multiprecision and C++11.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2021-02-15 22:06:29 UTC  
> Url: https://github.com/boostorg/math/pull/541#issuecomment-779468563  

There is a C++11 draft of the [new `e_float` bindings](https://github.com/boostorg/math/blob/e_float_2021/include/boost/math/bindings/e_float.hpp) available.  
  
Some preliminary tests running now on GCC and VC are [here](https://github.com/ckormanyos/e_float-2021/blob/main/libs/e_float/test_boost/test_boost.cpp). These tests exercise non-trivial calculations using `tgamma()` from math and a generalized Legendre function on the real axis in the unit disk. Tests to be extended include real concept (TBD).  
  
A plan for testing in CI still needs to be worked out. The `e_float` library requirec either pre-compilation of a lib or compilation on the spot of about 5 cpp files.

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2021-02-16 14:52:53 UTC  
> Updated_at: 2021-02-16 14:55:38 UTC  
> Url: https://github.com/boostorg/math/pull/541#issuecomment-779888076  

> Tests to be extended include real concept (TBD).  
  
So I've refined the `e_float` bindings to the extent where it's time to get something running on CI. There are several math tests and real-concept tests that I have run successfully on a few compilers locally.  
  
One of the archtectural questions is... How should I integrate the tests of the bindings in math? In order to compile, link and run, these tests need a library or objects resulting from compiling a few cpp from e_float. These files could either be built on the spot in CI or a pre-fab LIB(s) could be pulled from a pre-built LIB in my repo.  
  
Some ideas include:  
- In the CI of math, checkout the e_float-2021 repo, build the selected files together with math test code and run. I would not at the moment know exactly how to do each step of this in bjam right now, but that might be feasible.  
- Roll some pre-built, pre-fab LIB(s) and publish them in the e_float-2021 repo. Get them in math's CI for the math tests.  
- Do no testing of the `e_float` bindings whatsoever in Boost's CI and leave testing up to me in my own repo.  
  
Any thoughts on how to get this stuff tested?

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2021-02-21 07:12:35 UTC  
> Updated_at: 2021-02-21 07:38:24 UTC  
> Url: https://github.com/boostorg/math/pull/541#issuecomment-782812453  

> In the CI of math, checkout the e_float-2021 repo, build the selected files together with math test code and run.  
  
A prototype of this for testing `e_float-2021` bindings is now running in CI in this draft PR.  
  
At the moment, the jobs in the matrix run the _efx_ (native decimal base-10) and _gmp_ floating point backends on C++11,14,17,2a on g++ and clang++ on Ubuntu latest. The build commands are expressed in bash commands on the command line without the use of shell scripts and not explicitly using b2.  
  
This prototypes this general kind of CI, whereby more consistent use of b2 or shell scripts might be desired.  
  
A separate YAML has been created [here](https://github.com/boostorg/math/blob/e_float_2021/.github/workflows/e_float_boost_test.yml).  
  
Testing the `e_float` bindings includes tests of Boost.Math `tgamma`, sevel non-trivial manually programmed values of generalized Legendre functions of non-integral order and degree, and some basic concept checking. These tests are available [here](https://github.com/ckormanyos/e_float-2021/tree/main/libs/e_float/test_boost).  
  
Hello @jzmaddock and @mborland this is a neat opportunity to test some Multiprecision bindings in Math. It is, however, the first time that I personally checked out a different repo for some Boost testing, and that might be considered unusual. I'm not sure if you folks want to pursue this? Comments? Ideas? Feasibility judgements?

---

## Comment 4

> Username: mborland  
> Created_at: 2021-02-21 11:52:46 UTC  
> Url: https://github.com/boostorg/math/pull/541#issuecomment-782845249  

[This is the current list](https://github.com/mborland/math/issues/2) of dependencies in Boost.Math once PR #540 gets merged. I am actively stripping out dependencies so that Math can come closer to being consumed in isolation like Boost.JSON. The only current dependency on Boost.Multiprecision is a tool I added for benchmarking that can be removed since I am the only user. I think adding bindings for Multiprecision would be useful if they exist behind a guard that can disable them and the associated dependency if the end user doesn't need or want them. My failed attempt at implementing a prime sieve would have offered explicit bindings for multiprecision that would not have been hard to hide.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2021-02-21 17:25:30 UTC  
> Updated_at: 2021-02-21 17:26:08 UTC  
> Url: https://github.com/boostorg/math/pull/541#issuecomment-782892884  

> I am actively stripping out dependencies so that Math can come closer to being consumed in isolation like Boost.JSON. The only current dependency on Boost.Multiprecision is a tool I added for benchmarking that can be removed since I am the only user.  
  
That is great. Nonetheless, this is not exactly what I mean. This draft PR actually needs to build parts of the `e_float-2021` repo and link them with boost test code, as this legacy project is not header-only and not part of Boost. Thus it is so unusual to actually clone a third party repo (even if it is my own) for some Boost testing.  
  
My questions mean:  
  
Should we either:  
- Support `e_float`bindings.  
- Get something sensible running on CI.  
- Update any docs needed like [here](https://www.boost.org/doc/libs/1_75_0/libs/math/doc/html/math_toolkit/high_precision/e_float.html).  
  
Or..  
- Eliminate `e_float` bindings from code headers, docs and CI  
  
This PR goes for the former. I could live with either way, but having broken `e_float`bindings pointing to a non-supported project is a state worth recommended resolving.  
  
As to your dependency list, it's great! In fact, I could imagine making a little GHA that clones non-recursively, updates only those submodules needed, bootstraps, runs `b2 headers` and gets you ready to run CI using Boost.Math and Boost.Multiprecision. I just did these steps manually (probably including a few too many submodules, bot OK for now).

---

## Comment 6

> Username: mborland  
> Created_at: 2021-02-22 14:14:02 UTC  
> Url: https://github.com/boostorg/math/pull/541#issuecomment-783404319  

That is my mistake. I agree that having out of tree dependencies is far from ideal especially since it is not something I would be able to get through my system package manager like FFTW3. I would argue for removing the bindings here and calling it a day. If the repo the current bindings point to is unsupported I would assume there are not many users unless you are seeing issues and PRs against that repo.

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2021-02-22 14:43:45 UTC  
> Updated_at: 2021-02-22 14:45:39 UTC  
> Url: https://github.com/boostorg/math/pull/541#issuecomment-783423845  

> having out of tree dependencies is far from ideal  
  
I suggest the following compromise.  
  
- The new and supported [`e_float-2021` repo](https://github.com/ckormanyos/e_float-2021) supports Boost.Multiprecision bindings for `e_float` in [this directory](https://github.com/ckormanyos/e_float-2021/tree/main/libs/math/include/boost/math/bindings)  
- This newly supported Repo also performc CI steps with Boost.Math and Boost.Multiprecision develop branches [here](https://github.com/ckormanyos/e_float-2021/blob/main/.github/workflows/e_float_boost_test.yml)  
- Manage `e_float`bindings in my own repo, do CI in the same.  
- Eliminate `e_float` bindings from Boost.Math and modify the Boost.Math docs accordingly to mention them in the `e_float`repo.  
  
Agreed?

---

## Comment 8

> Username: mborland  
> Created_at: 2021-02-22 15:02:15 UTC  
> Url: https://github.com/boostorg/math/pull/541#issuecomment-783436343  

Agreed; that plan makes sense to me.

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2021-02-22 17:06:51 UTC  
> Url: https://github.com/boostorg/math/pull/541#issuecomment-783524807  

> Agreed; that plan makes sense to me.  
  
OK. Good. Then that's what we will do. I can handle this in this PR.

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2021-02-22 21:26:14 UTC  
> Updated_at: 2021-02-22 21:27:30 UTC  
> Url: https://github.com/boostorg/math/pull/541#issuecomment-783685702  

This recent push is intended to eliminate `e_float` explicit bindings and tests from Boost.Math.  
From now on, these will be handled in the [`e_float-2021` repository](https://github.com/ckormanyos/e_float-2021).  
Slight changes to docs (qbk) sources reflecting this migration are included in this PR.

---

## Comment 11

> Username: ckormanyos  
> Created_at: 2021-02-23 06:12:07 UTC  
> Url: https://github.com/boostorg/math/pull/541#issuecomment-783937082  

This is green now.  
  
In this PR, the `e_float` bindings (which are also now a full-fledged Multiprecision backend) are supported solely in the [`e_float-2021` repo](https://github.com/ckormanyos/e_float-2021).  
  
The docs in this checkin attempt to clarify the `e_float` bindings. These docs could be re-clafified or the small section on `e_float` could harmlessly be excluded altogether, depending on style or choice.  
  
Since the `e_float` bindings are actually now a Multiprecision backend, a note of them might be helpful in the multiprecision docs to assist others when trying to fulfill the Multiprecision backend requirements?  
  
Don't know if @jzmaddock has followed this discussion, but do you have any comments?

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2021-02-23 21:42:11 UTC  
> Url: https://github.com/boostorg/math/pull/541#issuecomment-784532595  

The docs regarding `e_float` bindings have been further reduced in scope and depth.  
  
I would like to finish this issue and this PR and would like to merge after drone cycles.  
  
Are there any rejections or futher comments or suggestions for improving technical content or clarity?

---

## Comment 13

> Username: ckormanyos  
> Created_at: 2021-02-24 19:22:48 UTC  
> Updated_at: 2021-02-24 19:23:38 UTC  
> Url: https://github.com/boostorg/math/pull/541#issuecomment-785315073  

I've re-opened this in #554, since I forgot to merge develop prior to merging the PR.  
Corrections are cycling in CI.

---
