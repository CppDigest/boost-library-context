# #700 Use 'obj' rule instead of 'compile' in opencl availability test. [Merged]

> Username: awulkiew  
> Created at: 2017-03-29 22:19:03 UTC  
> Updated at: 2017-03-30 07:16:28 UTC  
> Merged at: 2017-03-30 07:16:28 UTC  
> Closed at: 2017-03-30 07:16:28 UTC  
> Url: https://github.com/boostorg/compute/pull/700  

The use of 'compile' rule interferes with --dump-tests option of b2 and prevents correct running of tests in some of the other Boost libraries.  
  
See discussion: http://boost.2283326.n4.nabble.com/build-geometry-regression-Geometry-develop-regression-tests-are-blank-td4693186.html

---

## Comment 1

> Username: coveralls  
> Created_at: 2017-03-30 00:33:22 UTC  
> Url: https://github.com/boostorg/compute/pull/700#issuecomment-290267008  

[![Coverage Status](https://coveralls.io/builds/10839451/badge)](https://coveralls.io/builds/10839451)  
  
Coverage remained the same at 83.426% when pulling **f68111457df62ba936e3cf5dbc28eb3999758f0a on awulkiew:patch-1** into **6f9e6cf650f4fedb5670cfea2b3f70a584ccd8d2 on boostorg:develop**.

---
