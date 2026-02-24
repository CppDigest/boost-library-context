# #281 - Quadrature test fail after correcting typos [Closed]

> Username: pabristow  
> Created at: 2019-12-18 16:10:20 UTC  
> Updated at: 2019-12-20 16:50:07 UTC  
> Closed at: 2019-12-20 16:50:06 UTC  
> Url: https://github.com/boostorg/math/issues/281  

https://github.com/boostorg/math/commit/9029e747b4cca33c4ba81eec8a4bbbdee4ffb49c  
shows the changes made correcting typos found by Brian Wignall's program.  
  
I've searched for all mentions of quadrature in *.*pp files  
  
include/boost/math/quadrature/trapezoidal.hpp  
  
include/boost/math/quadrature/detail/tanh_sinh_detail.hpp  
  
have changes but they are only in comment or strings, so I don't see this as the cause.  
  
https://www.dropbox.com/s/czul53tibz3s4fx/.math_test_18dec2019.log?dl=0  
  
is the full test log  
  
I:\boost\libs\math\test>b2 -a > .math_test_18dec2019.log is command line  
  
MSVC 14.2, default C++ std = c++14 I think (but not sure).  
  
I suspect this test is running but it should not because requires something that isn't present.  
  
GCC libquadmath and __float128 support : no  
  
When (if) the testers get round to it, cause may become clearer?

---

## Comment 1

> Username: ckormanyos  
> Created at: 2019-12-18 20:19:36 UTC  
> Updated at: 2019-12-18 20:19:58 UTC  
> Url: https://github.com/boostorg/math/issues/281#issuecomment-567196779  

line 46 of:  
* boost/math/quadrature/trapezoidal.hpp  
  
...appears as if it has received an actual change in source code.  
I can not see a problem with the change. But maybe look there?  
... such as for a hidden character or something unusual?

---

## Comment 2

> Username: ckormanyos  
> Created at: 2019-12-19 08:15:48 UTC  
> Updated at: 2019-12-19 08:16:52 UTC  
> Url: https://github.com/boostorg/math/issues/281#issuecomment-567384533  

Paul,  
There are also more tangible errors listed in the log you posted in drop box.  
  
We find numerous messages such as:  
```  
I:\boost\boost/memory_order.hpp(57): error C2144: syntax error: 'boost::memory_order' should be preceded by ';'  
I:\boost\boost/memory_order.hpp(57): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
```  
  
I don't know exactly what version of Boost you are using to test. So I am unable to investigate the compiler switches that MSVC is detecting issues with. It seems like the file `memory_order.hpp` warrants further investigation.  
  
Kind regards, Chris

---

## Comment 3

> Username: pabristow  
> Created at: 2019-12-19 10:25:29 UTC  
> Url: https://github.com/boostorg/math/issues/281#issuecomment-567430366  

The changes in include/boost/math/quadrature/trapezoidal.hpp are only in the error message string as far as I can see.  
  
I've just used develop branch to test - but foolishly I didn't test before making the change. Perhaps I should go back to pre-typo removal/

---

## Comment 4

> Username: pabristow  
> Created at: 2019-12-20 16:50:06 UTC  
> Url: https://github.com/boostorg/math/issues/281#issuecomment-567991803  

I have wound back to 8c9fc93094003a8fbc396da90fb5f8ecf7560e50 before the typo changes with the same result. So the typos are very probably NOT the cause.  
  
I suspect this is more likely an up-coming problem with the atomic library using the latest MSVC Preview compiler.  The regression tester do not yet use this version.
