# #720 Add MSVC-2022 to CI Testing [Merged]

> Username: mborland  
> Created at: 2021-11-21 15:23:40 UTC  
> Updated at: 2025-12-04 10:23:25 UTC  
> Merged at: 2022-02-06 09:14:46 UTC  
> Closed at: 2022-02-06 09:14:46 UTC  
> Url: https://github.com/boostorg/math/pull/720  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-11-21 16:48:39 UTC  
> Url: https://github.com/boostorg/math/pull/720#issuecomment-974853173  

Does the one support modules?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-11-21 16:57:03 UTC  
> Url: https://github.com/boostorg/math/pull/720#issuecomment-974854561  

> Does the one support modules?  
  
Yes, as does the existing VS2019.

---

## Comment 3

> Username: mborland  
> Created_at: 2021-11-22 15:52:04 UTC  
> Url: https://github.com/boostorg/math/pull/720#issuecomment-975664452  

@jzmaddock As the MSVC guru does the failure in `test_next` make sense to you? Some kind of change to default rounding mode in the new release?

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-11-22 16:50:15 UTC  
> Url: https://github.com/boostorg/math/pull/720#issuecomment-975722570  

No, but I see it's officially released now so I'm downloading as I speak...  
  
BTW, C++11 testing is redundant for msvc - it only supports C++14/17/20.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2021-11-22 19:46:20 UTC  
> Url: https://github.com/boostorg/math/pull/720#issuecomment-975862101  

I'm pretty much stumped on this, I'm unable to reproduce locally at all (which is to say all tests pass with msvc-14.3, compiler version matches that in the test suite).  Can anyone else have a try?

---

## Comment 6

> Username: mborland  
> Created_at: 2021-11-25 09:19:25 UTC  
> Url: https://github.com/boostorg/math/pull/720#issuecomment-979004967  

I installed a copy of Visual Studio 2022, and am also unable to reproduce this error locally.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2022-02-02 18:38:50 UTC  
> Url: https://github.com/boostorg/math/pull/720#issuecomment-1028240755  

Now that VS2022 is released, lets kick this off again....

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2022-02-05 11:21:20 UTC  
> Url: https://github.com/boostorg/math/pull/720#issuecomment-1030603596  

Matt the failures here I've just been hit with in multiprecision as well: the meaning of "windows-latest" has just changed, we need to change that to "windows-2019", otherwise everything breaks! :(

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2022-02-05 11:24:02 UTC  
> Url: https://github.com/boostorg/math/pull/720#issuecomment-1030603929  

Never mind, apparently I can commit direct to this branch via the web-interface, CI updated and we'll see what happens.

---

## Comment 10

> Username: NAThompson  
> Created_at: 2022-02-05 16:50:33 UTC  
> Url: https://github.com/boostorg/math/pull/720#issuecomment-1030657790  

> Never mind, apparently I can commit direct to this branch via the web-interface, CI updated and we'll see what happens.  
  
@jzmaddock : Try `gh pr checkout 720` using [this](https://cli.github.com/manual/gh); I believe that Matt always clicks the "allow edits from maintainers" button so it's easy to collaborate.

---

## Comment 11

> Username: mbs-c  
> Created_at: 2025-12-02 12:58:08 UTC  
> Url: https://github.com/boostorg/math/pull/720#issuecomment-3601908355  

> I'm pretty much stumped on this, I'm unable to reproduce locally at all (which is to say all tests pass with msvc-14.3, compiler version matches that in the test suite). Can anyone else have a try?  
  
@jzmaddock   
We just ran into the very same problem in our own builds, with MSVC 19.29, so the current preprocessor logic to disable the test for newer compilers is insufficient.  
  
According to our tests, the error only occurs when running the test in a Windows docker container, which might explain why you were never able to reproduce it. If anyone has any idea how to debug this further, I'd be happy to help.

---

## Comment 12

> Username: mbs-c  
> Created_at: 2025-12-02 15:40:28 UTC  
> Updated_at: 2025-12-02 16:24:54 UTC  
> Url: https://github.com/boostorg/math/pull/720#issuecomment-3602669333  

For the sake of completeness, the docker image I used for testing is based on Server Core LTSC 2022 and the test errors I'm seeing inside the container are the following:  
  
```  
Testing type real_concept with initial value 2.22507e-308  
.../test_next.cpp(43): error: in "test_main": check float_distance(float_prior(val), val) == 1 has failed [2 != 1]  
.../test_next.cpp(47): error: in "test_main": check float_distance((boost::math::nextafter)(val, lower), val) == 1 has failed [2 != 1]  
.../test_next.cpp(58): error: in "test_main": check float_distance(float_advance(val, -4), val) == 4 has failed [2 != 4]  
Testing type real_concept with initial value -2.22507e-308  
.../test_next.cpp(41): error: in "test_main": check float_distance(float_next(val), val) == -1 has failed [-2 != -1]  
.../test_next.cpp(45): error: in "test_main": check float_distance((boost::math::nextafter)(val, upper), val) == -1 has failed [-2 != -1]  
.../test_next.cpp(57): error: in "test_main": check float_distance(float_advance(val, 4), val) == -4 has failed [-2 != -4]  
```  
  
**Edit**: Also, building inside the container and running the test outside the container works, so the test failure must be caused by the runtime environment inside the container.

---

## Comment 13

> Username: mbs-c  
> Created_at: 2025-12-04 10:23:25 UTC  
> Url: https://github.com/boostorg/math/pull/720#issuecomment-3611354235  

I investigated a bit further and the root cause seems to be that `ldexp(long double, int)`, which is called from `float_prior_imp`, returns different values. When called from `test_next`, `std::ldexp(1.0L, -1075)` returns `4.94066e-324` inside the Windows container and `0` outside.  
  
In both cases, the `real_concept` test case prints "Denormals are flushed to zero." in the beginning.

---
