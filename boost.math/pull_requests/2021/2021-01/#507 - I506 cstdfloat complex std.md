# #507 I506 cstdfloat complex std [Merged]

> Username: ckormanyos  
> Created at: 2021-01-31 14:01:50 UTC  
> Updated at: 2024-01-24 21:34:14 UTC  
> Merged at: 2021-08-31 04:58:21 UTC  
> Closed at: 2021-08-31 04:58:21 UTC  
> Url: https://github.com/boostorg/math/pull/507  

This PR addresses #506 via creation of corrected code and new test cases intended to test the corrected behavior.  
For initial testing purpoese, the YAML code has been reduced and should not yet be merged in this form

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2021-01-31 15:03:43 UTC  
> Url: https://github.com/boostorg/math/pull/507#issuecomment-770396031  

> creation of corrected code and new test cases   
  
So far so good on this issue. The fix is anticipated to be relatively straightforward.  
  
...But @mborland the actual float128 tests I'd like to exercise on GHA are not (or seem like they're not) running on GHA. I did significantly reduce the YAML script to just a few cases and compilers, but I would have thought the default Ubuntu setup has libquadmath. Do you see anything I missed in YAML?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-01-31 16:13:51 UTC  
> Url: https://github.com/boostorg/math/pull/507#issuecomment-770406268  

I need to check, but I suspect the tests are being run with -std=c++XX rather than -std=gnu++XX.  We need GNU extensions on for __float128.

---

## Comment 3

> Username: mborland  
> Created_at: 2021-01-31 16:14:56 UTC  
> Url: https://github.com/boostorg/math/pull/507#issuecomment-770406455  

@ckormanyos The [config shows](https://github.com/boostorg/math/pull/507/checks?check_run_id=1801296683#step:5:16) libquadmath is provided by the base Ubuntu install. Is there another dependency for these tests?

---

## Comment 4

> Username: mborland  
> Created_at: 2021-01-31 17:31:11 UTC  
> Url: https://github.com/boostorg/math/pull/507#issuecomment-770418243  

@jzmaddock None of the tests in the Actions YAML file use gnu++XX. Do some need to get added in for additional variety?

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2021-01-31 17:57:13 UTC  
> Url: https://github.com/boostorg/math/pull/507#issuecomment-770422155  

> None of the tests in the Actions YAML file use gnu++XX. Do some need to get added in for additional variety?  
  
I was thinking we should probably add a drone CI config to back up GHA, we could make that all gnu++ by way of variety?

---

## Comment 6

> Username: mborland  
> Created_at: 2021-01-31 18:38:25 UTC  
> Url: https://github.com/boostorg/math/pull/507#issuecomment-770428583  

> I was thinking we should probably add a drone CI config to back up GHA, we could make that all gnu++ by way of variety?  
  
It would probably be wise to split the load depending on how fast drone returns results. I do appreciate that GHA knocks out the current test battery in under two hours. My run testing architectures on Travis is still pending from last week...

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2021-01-31 18:44:02 UTC  
> Url: https://github.com/boostorg/math/pull/507#issuecomment-770429318  

> It would probably be wise to split the load depending on how fast drone returns results. I do appreciate that GHA knocks out the current test battery in under two hours. My run testing architectures on Travis is still pending from last week...  
  
Drone takes a little longer to pull down the images and get started, but the actual runs are very fast, multiprecision is cycling both drone and GHA in an hour or two.  
  
Travis is currently dead to us: the boostorg account has been suspended for using up too many cycles, it might start up again at some point or not.  I've also just disabled a bunch of libraries that have moved to drone in Travis, I fear might have accidentally killed your PR in the process :(

---

## Comment 8

> Username: mborland  
> Created_at: 2021-01-31 18:54:44 UTC  
> Url: https://github.com/boostorg/math/pull/507#issuecomment-770430951  

> Drone takes a little longer to pull down the images and get started, but the actual runs are very fast, multiprecision is cycling both drone and GHA in an hour or two.  
  
Sounds like it would be worthwhile to use as backup or continue expanding the tests to include gnu standards on.  
   
> Travis is currently dead to us: the boostorg account has been suspended for using up too many cycles, it might start up again at some point or not. I've also just disabled a bunch of libraries that have moved to drone in Travis, I fear might have accidentally killed your PR in the process :(  
  
I can just turn that PR into a remove travis PR then; at that rate it wouldn't offer any useful results anyway. One of the Kernel devs has [this repo](https://github.com/groeck/linux-build-test) for testing the kernel on qemu. It may be worthwhile forking it and making it work for testing boost. There does not seem to be any particularly good CI options for testing non x86_64 architectures.

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2021-01-31 19:05:03 UTC  
> Url: https://github.com/boostorg/math/pull/507#issuecomment-770432473  

> add a drone CI config to back up GHA, we could make that all gnu++ by way of variety?  
  
Yes. Agree with the entire dialog. I will comply in these and the Multiprecision PR(s). At first keep all compilers active, and make a judicious selection at a later point when the load-splitting becomes more clear how to do it...

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2021-01-31 19:09:50 UTC  
> Url: https://github.com/boostorg/math/pull/507#issuecomment-770433177  

> Sounds like it would be worthwhile to use as backup or continue expanding the tests to include gnu standards on.  
  
Let me see what I can rustle up.

---

## Comment 11

> Username: mborland  
> Created_at: 2021-01-31 19:13:53 UTC  
> Url: https://github.com/boostorg/math/pull/507#issuecomment-770433848  

@jzmaddock The drone docs shows support for at least [Linux and BSD on ARM](https://docs.drone.io/pipeline/exec/syntax/platform/). I can't imagine we need to do much i386 testing.

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2021-01-31 19:19:01 UTC  
> Url: https://github.com/boostorg/math/pull/507#issuecomment-770434925  

> The drone docs shows support for at least Linux and BSD on ARM. I can't imagine we need to do much i386 testing.  
  
Drone is open source and entirely self-hosted, the CppAllience who are providing the service only has x64 clients set up.  But yes Drone itself can handle almost any platform and architecture if there's someone to provide the client machines.

---

## Comment 13

> Username: ckormanyos  
> Created_at: 2021-01-31 21:14:43 UTC  
> Url: https://github.com/boostorg/math/pull/507#issuecomment-770452078  

> for testing the kernel on qemu. It may be worthwhile forking it and making it work for testing boost. There does not seem to be any particularly good CI options for testing non x86_64 architectures.  
  
I can look into something like that. Cross compiling is straightforward. Execution on a runner in a simulator is expected to be slow.  
  
As for the original topic in this PR, using the compiler flag `-gnu++11` or similar does activate libquadmath. But I do find some other interesting problems. I'll try to sort out what's all happening and report soon.  
  
The original point should be fixed in the header. But I find unrelated potential issues.

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2021-02-01 10:27:31 UTC  
> Url: https://github.com/boostorg/math/pull/507#issuecomment-770750098  

@ckormanyos : Please be aware that the drone CI is still cycling it's first run and is showing up some new failures (mostly Jamfile errors spotted so far), I'll get to those later when the whole run has completed.

---

## Comment 15

> Username: ckormanyos  
> Created_at: 2021-02-01 10:36:00 UTC  
> Url: https://github.com/boostorg/math/pull/507#issuecomment-770755323  

> Please be aware that the drone CI is still cycling it's first run and is showing up some new failures (mostly Jamfile errors spotted so far)  
  
Understood. Let's see how it goes.  
  
The changes in this particular branch here are limited in scope. So uless some major problems arise in drone, I'll simply keep this branch synchronized with drone's progress and we can handle the merge of the PR as we deem fit after that all stabilizes.

---

## Comment 16

> Username: ckormanyos  
> Created_at: 2021-02-05 19:34:57 UTC  
> Updated_at: 2021-02-07 09:19:57 UTC  
> Url: https://github.com/boostorg/math/pull/507#issuecomment-774244353  

Reformulate the change request of issue #506  
1. Selected functions like complex `log`, `sqrt`, `pow`, etc. to handle zeros, NaNs and INFs better (or less wrongly).  
2. Thereby, let C99 Annex G be the guide to those special values resulting from (1).  
3.  Retain the individual specializations for `float`, `double` and `long double`.  
4. Additional overloads, insofar as needed, could be handled with `enable_if` now that C++11 is used in Math.  
5. Use `constexpr` (in the form of `BOOST_CXX14_CONSTEXPR`) declarations in front of selected [member]-functions where appropriate.  
6. Check to potentially eliminate dependency on `<boost/math/constants.hpp>`.

---

## Comment 17

> Username: jzmaddock  
> Created_at: 2021-02-07 08:24:10 UTC  
> Url: https://github.com/boostorg/math/pull/507#issuecomment-774633641  

One other suggestion - we should scatter some BOOST_CXX14_CONSTEXPR declarations in front of the [member]-functions that are constexpr in <complex> C++20.

---

## Comment 18

> Username: ckormanyos  
> Created_at: 2021-02-07 09:17:35 UTC  
> Url: https://github.com/boostorg/math/pull/507#issuecomment-774640515  

> One other suggestion - we should scatter some BOOST_CXX14_CONSTEXPR declarations in front of the [member]-functions that are constexpr in C++20.  
  
Right, as does C++14 `<complex>` itself. I have modified (via edit) the list above.  
Thanks John.  
  
I would also like to see if there is a way to eliminate the dependency on `<boost/math/constants.hpp>`, which I think is rreally only used for the constant value of `log(10)`.

---

## Comment 19

> Username: ckormanyos  
> Created_at: 2021-02-25 06:08:33 UTC  
> Updated_at: 2021-02-25 06:09:38 UTC  
> Url: https://github.com/boostorg/math/pull/507#issuecomment-785640152  

I will be picking up work on this issue again. In the intermediate time, a lot of progress was made on CI, and the migrations to C++11 in Math and Multiprecision. So I'll also be reviewing the top-level requirements of this issue and simlutaneously attempting to ensure that the direction of progress is consistent with the new stands of Math and Multiprecision.

---

## Comment 20

> Username: ckormanyos  
> Created_at: 2021-08-31 04:57:46 UTC  
> Url: https://github.com/boostorg/math/pull/507#issuecomment-908898812  

This PR leaves `<boost/cstdfloat.hpp>` and its complex functions (also 128-bit) in generally a better state than before and addresses the original point and a few more edge cases. There are probably a few more edge cases undiscovered. But I'm merging this, as it's gotten better and going green on CI.

---
