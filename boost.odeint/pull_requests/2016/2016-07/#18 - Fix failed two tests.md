# #18 Fix failed two tests. [Merged]

> Username: Flast  
> Created at: 2016-07-30 08:02:55 UTC  
> Updated at: 2016-08-09 13:11:48 UTC  
> Merged at: 2016-08-09 03:33:49 UTC  
> Closed at: 2016-08-09 03:33:49 UTC  
> Url: https://github.com/boostorg/odeint/pull/18  



---

## Comment 1

> Username: mariomulansky  
> Created_at: 2016-08-05 05:50:50 UTC  
> Url: https://github.com/boostorg/odeint/pull/18#issuecomment-237761840  

Can you shortly comment on where this unit test fails (OS, Compiler, Version). This was added in response to https://github.com/headmyshoulder/odeint-v2/issues/149. Are you compiling with msvc as well?

---

## Comment 2

> Username: Flast  
> Created_at: 2016-08-05 09:58:16 UTC  
> Url: https://github.com/boostorg/odeint/pull/18#issuecomment-237811712  

> Can you shortly comment on where this unit test fails (OS, Compiler, Version).  
  
Oh, sorry that. I used GCC6 (fedora rawhide, I forget detail version...) with gnu++98/11/14 mode.  
  
The change `test/numeric/order_quadrature_formula.cpp` is for [teeks99-02-dg4.4-g98-Docker-64on64 - numeric/odeint - order_quadrature_formula / gcc-4.4~gnu98](http://www.boost.org/development/tests/develop/developer/output/teeks99-02-dg4-4-g98-Docker-64on64-boost-bin-v2-libs-numeric-odeint-test-numeric-order_quadrature_formula-test-gcc-4-4~gnu98-debug-link-static.html) and other same errors.  
And other changes are for [teeks99-02-dg4.4-98-Docker-64on64 - numeric/odeint - regression_149 / gcc-4.4~c++98](http://www.boost.org/development/tests/develop/developer/output/teeks99-02-dg4-4-98-Docker-64on64-boost-bin-v2-libs-numeric-odeint-test-regression-regression_149-test-gcc-4-4~c++98-debug-link-static.html).  
  
> This was added in response to headmyshoulder/odeint-v2#149. Are you compiling with msvc as well?  
  
I didn't know about that (and didn't tested on MSVC), but I assume the test is not necessary to use lambda and auto. I'll test MSVC and report the results.

---

## Comment 3

> Username: mariomulansky  
> Created_at: 2016-08-08 04:53:43 UTC  
> Url: https://github.com/boostorg/odeint/pull/18#issuecomment-238141645  

As for the missing include, this is an obvious mistake and should be fixed. For the regression I would prefer to leave it as is. I think it is acceptable if this fails for C++03 only compilers. But maybe C++11 mode was needed to observe the bug in the first place so changing the code into C++03 might defeat the whole purpose of this test.

---

## Comment 4

> Username: Flast  
> Created_at: 2016-08-08 15:05:06 UTC  
> Url: https://github.com/boostorg/odeint/pull/18#issuecomment-238266426  

I tested a) develop , b) without f312a809c86387dfe948a826625643f3186fa760 , c) a with this PR and d) b with this PR on MSVC 10,11,12 and 14.  
All compilers show same result with and without this PR: i.e. c++11 mode is unnecessary to reproduce.

---

## Comment 5

> Username: mariomulansky  
> Created_at: 2016-08-09 03:33:44 UTC  
> Url: https://github.com/boostorg/odeint/pull/18#issuecomment-238444475  

Ok, thanks for checking. Out of curiosity - does this regression still fail on MSVC? I don't have a windows machine for testing, unfortunately...

---

## Comment 6

> Username: Flast  
> Created_at: 2016-08-09 13:11:44 UTC  
> Url: https://github.com/boostorg/odeint/pull/18#issuecomment-238547804  

> Out of curiosity - does this regression still fail on MSVC?  
  
No. f312a80 fixes the regression.

---
