# #622 Fix adjacent_difference when first == result [Merged]

> Username: jszuppe  
> Created at: 2016-07-01 19:29:57 UTC  
> Updated at: 2016-07-06 07:54:30 UTC  
> Merged at: 2016-07-05 23:52:40 UTC  
> Closed at: 2016-07-05 23:52:40 UTC  
> Url: https://github.com/boostorg/compute/pull/622  

Situation where `first == result` was only covered when binary function was not specified.

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-07-01 20:43:06 UTC  
> Url: https://github.com/boostorg/compute/pull/622#issuecomment-230043174  

[![Coverage Status](https://coveralls.io/builds/6841197/badge)](https://coveralls.io/builds/6841197)  
  
Coverage decreased (-0.003%) to 80.576% when pulling **92a16e459c12bf997f5097901614ccf31875d680 on haahh:pr_adjacent_diff_fix** into **e5a9017d6952f97e7d4723f34aa6264106e3303e on boostorg:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2016-07-05 23:52:36 UTC  
> Url: https://github.com/boostorg/compute/pull/622#issuecomment-230636199  

Looks good! We should probably also add a test for the `first == last` case.

---
