# #244 Add read() and write() methods to iterators [Merged]

> Username: kylelutz  
> Created at: 2014-08-20 06:54:32 UTC  
> Updated at: 2014-11-20 06:26:59 UTC  
> Merged at: 2014-11-20 06:24:18 UTC  
> Closed at: 2014-11-20 06:24:18 UTC  
> Url: https://github.com/boostorg/compute/pull/244  



---

## Comment 1

> Username: kylelutz  
> Created_at: 2014-08-20 06:56:19 UTC  
> Url: https://github.com/boostorg/compute/pull/244#issuecomment-52740312  

@roshanr95 this should remove the need to use `detail::read_single_value()` and simplify code that used it before. can you look over the changes to your algorithms and make sure I've updated them correctly?

---

## Comment 2

> Username: coveralls  
> Created_at: 2014-08-20 07:15:48 UTC  
> Url: https://github.com/boostorg/compute/pull/244#issuecomment-52741675  

[![Coverage Status](https://coveralls.io/builds/1106750/badge)](https://coveralls.io/builds/1106750)  
  
Coverage remained the same when pulling **504dd21b68a6d45e078a180c62d2c568ccc03700 on iterator-read-write** into **406c38b0f16c1effd53ede31bec1e276418e2bf5 on develop**.

---

## Comment 3

> Username: coveralls  
> Created_at: 2014-11-20 06:26:59 UTC  
> Url: https://github.com/boostorg/compute/pull/244#issuecomment-63766502  

[![Coverage Status](https://coveralls.io/builds/1501092/badge)](https://coveralls.io/builds/1501092)  
  
Coverage increased (+0.0%) when pulling **f53a2b3a8de99d8e99e6edcc1f3da904757a6997 on iterator-read-write** into **7ffd2224e892ac261cbd4b3b6bb2434c9e21291c on develop**.

---
