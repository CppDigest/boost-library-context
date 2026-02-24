# #385 Fix reciprocal of min for mpfr/cpp_bin_float in continued fractions. [Merged]

> Username: NAThompson  
> Created at: 2020-06-29 17:02:54 UTC  
> Updated at: 2020-07-01 01:01:41 UTC  
> Merged at: 2020-07-01 01:01:37 UTC  
> Closed at: 2020-07-01 01:01:37 UTC  
> Url: https://github.com/boostorg/math/pull/385  

The issue is identified [here](https://github.com/boostorg/multiprecision/issues/253).  
  
The issue can be reproduced by calling `continued_fraction_b` with b0 = 0 and a mpfr result_type.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2020-06-29 20:32:08 UTC  
> Url: https://github.com/boostorg/math/pull/385#issuecomment-651345947  

@jzmaddock : Could you review?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-06-30 08:17:14 UTC  
> Url: https://github.com/boostorg/math/pull/385#issuecomment-651634104  

That looks reasonable to me: can you just add a comment to the code so we know why the *16 was added?

---

## Comment 3

> Username: NAThompson  
> Created_at: 2020-06-30 14:07:14 UTC  
> Url: https://github.com/boostorg/math/pull/385#issuecomment-651814489  

@jzmaddock : Good catch; done.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2020-07-01 01:01:32 UTC  
> Url: https://github.com/boostorg/math/pull/385#issuecomment-652125761  

Build failure from serialization and a timeout.

---
