# #492 Fix for MSVC CI Failures [Merged]

> Username: mborland  
> Created at: 2021-01-14 18:12:43 UTC  
> Updated at: 2021-01-18 14:40:55 UTC  
> Merged at: 2021-01-18 14:38:50 UTC  
> Closed at: 2021-01-18 14:38:50 UTC  
> Url: https://github.com/boostorg/math/pull/492  

Should reduce noise from the CI system while waiting for https://github.com/boostorg/random/pull/76 to be merged

---

## Comment 1

> Username: mborland  
> Created_at: 2021-01-15 13:20:56 UTC  
> Url: https://github.com/boostorg/math/pull/492#issuecomment-760938400  

@NAThompson This suppressed a good number of the Windows failures. Previously all MSVC versions and language standards were failing for the `../tools` tests

---

## Comment 2

> Username: mborland  
> Created_at: 2021-01-17 18:22:38 UTC  
> Url: https://github.com/boostorg/math/pull/492#issuecomment-761856689  

@NAThompson Do you have a solution for issue #465? If it is just increasing the tolerance I can throw that in here. Once that is fixed CI should finally be green.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-01-17 18:34:33 UTC  
> Url: https://github.com/boostorg/math/pull/492#issuecomment-761858642  

> Do you have a solution for issue #465? If it is just increasing the tolerance I can throw that in here. Once that is fixed CI should finally be green.  
  
Has the code been fed through `std_real_concept` to check there are no mistakenly called double precision std library functions?

---

## Comment 4

> Username: NAThompson  
> Created_at: 2021-01-17 18:56:54 UTC  
> Url: https://github.com/boostorg/math/pull/492#issuecomment-761861694  

Yeah I need to actually think about that problem; will try to get to it soon!

---

## Comment 5

> Username: mborland  
> Created_at: 2021-01-17 19:08:43 UTC  
> Url: https://github.com/boostorg/math/pull/492#issuecomment-761863523  

I merged develop just to verify, but I believe this PR resolves all the outstanding CI failures except the two stemming from that issue.

---

## Comment 6

> Username: evanmiller  
> Created_at: 2021-01-18 03:26:45 UTC  
> Url: https://github.com/boostorg/math/pull/492#issuecomment-761955530  

@mborland Looking at the logs, the two `ooura_fourier_integral_test` failures appear to be unrelated. The Windows failure looks like another `bigobj` problem, so you should be able to fix it the same way the Daubechies tests were fixed.  
  
However, it looks like the big object problems can be fixed with `-Wa,-mbig-obj` instead of skipping the compilation via ifdef. This is already done for a few tests, see e.g.  
  
https://github.com/boostorg/math/blob/develop/test/Jamfile.v2#L490  
  
Might be worth investigating if you haven't done so already.

---

## Comment 7

> Username: mborland  
> Created_at: 2021-01-18 06:03:52 UTC  
> Updated_at: 2021-01-18 07:54:52 UTC  
> Url: https://github.com/boostorg/math/pull/492#issuecomment-762004170  

@evanmiller Good call. The jamfile already had `/bigobj` for `daubechies_scaling_test` and `daubechies_wavelet_test` using MSVC; just needed to add it for mingw runs.  
  
Edit: Even with `-Wa, -mbig-obj` added to the Jamfile mingw fails for big object.

---

## Comment 8

> Username: mborland  
> Created_at: 2021-01-18 11:17:10 UTC  
> Url: https://github.com/boostorg/math/pull/492#issuecomment-762181639  

This should be good to go. Only two remaining errors are issue #465.

---
