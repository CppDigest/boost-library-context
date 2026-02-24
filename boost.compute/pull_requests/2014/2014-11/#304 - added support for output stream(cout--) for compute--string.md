# #304 added support for output stream(cout<<) for compute::string [Merged]

> Username: Mageswaran1989  
> Created at: 2014-11-08 20:19:28 UTC  
> Updated at: 2014-11-08 21:53:56 UTC  
> Merged at: 2014-11-08 21:50:38 UTC  
> Closed at: 2014-11-08 21:50:38 UTC  
> Url: https://github.com/boostorg/compute/pull/304  

Please review.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2014-11-08 20:33:36 UTC  
> Updated_at: 2014-11-08 21:39:21 UTC  
> Url: https://github.com/boostorg/compute/pull/304#discussion_r20054235  

Instead of including all of `<ostream>`, it's usually better to just include `<iosfwd>` which just contains forward-declarations for classes like `std::ostream`. This helps improve compile times.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-11-08 20:35:51 UTC  
> Updated_at: 2014-11-08 21:39:21 UTC  
> Url: https://github.com/boostorg/compute/pull/304#discussion_r20054251  

Looks like there's a typo here: `Triats` -> `Traits`. Also, `basic_string` has two template parameters (`CharT` and `Traits`) so you should use both here.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-11-08 20:36:50 UTC  
> Updated_at: 2014-11-08 21:39:21 UTC  
> Url: https://github.com/boostorg/compute/pull/304#discussion_r20054260  

Cool! I didn't know about this. Looks useful!

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-11-08 20:37:12 UTC  
> Updated_at: 2014-11-08 21:39:21 UTC  
> Url: https://github.com/boostorg/compute/pull/304#discussion_r20054262  

Typo: `outStrem` -> `outStream`

---

## Comment 5

> Username: kylelutz  
> Created_at: 2014-11-08 20:38:26 UTC  
> Updated_at: 2014-11-08 21:39:21 UTC  
> Url: https://github.com/boostorg/compute/pull/304#discussion_r20054275  

You should probably take `outStr` by const-reference here.

---

## Comment 6

> Username: kylelutz  
> Created_at: 2014-11-08 20:39:33 UTC  
> Updated_at: 2014-11-08 21:39:21 UTC  
> Url: https://github.com/boostorg/compute/pull/304#discussion_r20054280  

Can you indent with four spaces?

---

## Comment 7

> Username: kylelutz  
> Created_at: 2014-11-08 20:39:36 UTC  
> Url: https://github.com/boostorg/compute/pull/304#issuecomment-62277884  

Looks good. Left a few minor notes. After that it should be good to merge.

---

## Comment 8

> Username: coveralls  
> Created_at: 2014-11-08 21:53:56 UTC  
> Url: https://github.com/boostorg/compute/pull/304#issuecomment-62280333  

[![Coverage Status](https://coveralls.io/builds/1447504/badge)](https://coveralls.io/builds/1447504)  
  
Coverage increased (+0.01%) when pulling **4b50a5c1dc6b6f5371cea9744a28d10b840c0e80 on Mageswaran1989:cout_support_string_class** into **0bdcf3601a27b5115fc4d668e60491f8299965c5 on kylelutz:develop**.

---
