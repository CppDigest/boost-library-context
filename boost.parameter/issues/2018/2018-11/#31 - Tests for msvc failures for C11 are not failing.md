# #31 - Tests for msvc failures for C11 are not failing [Closed]

> Username: eldiener  
> Created at: 2018-11-16 21:37:12 UTC  
> Updated at: 2018-11-22 20:29:44 UTC  
> Closed at: 2018-11-22 20:29:44 UTC  
> Url: https://github.com/boostorg/parameter/issues/31  

The parameter_msvc11_fail_tests is not failing for msvc-14.1, msvc-14.0, and msvc-12.0. The test does fail for msvc-11.0, but this failure may be because of a bug in msvc-11.0 and not because the logic correctly causes failure.

---

## Comment 1

> Username: CromwellEnage  
> Created at: 2018-11-17 16:49:05 UTC  
> Url: https://github.com/boostorg/parameter/issues/31#issuecomment-439630592  

I've changed test/Jamfile.v2 so that the compose_msvc11_fail_11 test runs only for msvc-11.0.  I double-checked the AppVeyor logs from my fork to ensure that the compose_msvc11_fail_11 test wasn't running for msvc-14.1, msvc-14.0, and msvc-12.0.

---

## Comment 2

> Username: eldiener  
> Created at: 2018-11-17 18:00:10 UTC  
> Url: https://github.com/boostorg/parameter/issues/31#issuecomment-439635664  

I did not realize that the "C11" in the name referred to a failure on msvc-11.0. I thought it referred to a test for msvc when c++11 mode on up was being used. I am glad that you have fixed this for running only against msvc-11.0.
