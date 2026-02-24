# #489 - CI Failure in makima_test [Closed]

> Username: mborland  
> Created at: 2021-01-13 16:38:59 UTC  
> Updated at: 2021-01-15 13:32:54 UTC  
> Closed at: 2021-01-15 13:32:54 UTC  
> Url: https://github.com/boostorg/math/issues/489  

Reported by @NAThompson but moved from the PR into issues:  
  
```  
makima_test.cpp:168:26:   required from here  
../../../boost/math/interpolators/makima.hpp:26:80: internal compiler error: in dependent_type_p, at cp/pt.c:23654  
            Real left_endpoint_derivative = std::numeric_limits<Real>::quiet_NaN(),  
                                            ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~  
Please submit a full bug report,  
with preprocessed source if appropriate.  
See <file:///usr/share/doc/gcc-7/README.Bugs> for instructions.  
```  
  
Looking at the travis test battery only g++-5, 6, and 8 were tested. To suppress this issue I can add the flag `-ffinite-math-only` but it would not solve the underlying issue. Thoughts?

---

## Comment 1

> Username: NAThompson  
> Created at: 2021-01-13 17:48:25 UTC  
> Url: https://github.com/boostorg/math/issues/489#issuecomment-759612476  

Is the compiler running out of memory on the CI system? This might be a random failure . . .

---

## Comment 2

> Username: mborland  
> Created at: 2021-01-13 18:12:04 UTC  
> Url: https://github.com/boostorg/math/issues/489#issuecomment-759626370  

@NAThompson I looked through the runs of the CI system on my fork, and this error pops up consistently. I would file a report on the GCC bugzilla but only 8, 9, and 10 are supported and there are no issues with those.

---

## Comment 3

> Username: NAThompson  
> Created at: 2021-01-13 20:31:18 UTC  
> Url: https://github.com/boostorg/math/issues/489#issuecomment-759721718  

@jzmaddock : I'd prefer to just support gcc's supported set on our own CI. What's your opinion?

---

## Comment 4

> Username: jzmaddock  
> Created at: 2021-01-14 11:38:40 UTC  
> Url: https://github.com/boostorg/math/issues/489#issuecomment-760142650  

Since it's just this test, I would prefer to continue testing with everything post gcc-6, maybe even gcc-5 as it has good C++11 support, and just mark this test as failing (add some pp-logic to the test to disable it for old gcc versions).
