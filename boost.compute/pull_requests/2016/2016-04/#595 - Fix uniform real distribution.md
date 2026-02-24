# #595 Fix uniform real distribution  [Merged]

> Username: jszuppe  
> Created at: 2016-04-24 19:08:08 UTC  
> Updated at: 2016-04-26 07:16:57 UTC  
> Merged at: 2016-04-26 04:35:23 UTC  
> Closed at: 2016-04-26 04:35:23 UTC  
> Url: https://github.com/boostorg/compute/pull/595  

Related to https://github.com/boostorg/compute/issues/587.   
  
Now generated floating-point values are uniformly distributed on the interval `[a, b)` and no value is greater or equal to `b`.  
  
I also added test that checks if results are in fact in `[a,b)` range (by using `range_test_engine` class that generates max random value or zeroes).

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-04-24 21:15:22 UTC  
> Url: https://github.com/boostorg/compute/pull/595#issuecomment-214038359  

[![Coverage Status](https://coveralls.io/builds/5911826/badge)](https://coveralls.io/builds/5911826)  
  
Coverage increased (+0.03%) to 88.821% when pulling **5a987feea8080e82a84bedf99944aca625c20386 on haahh:pr_fix_uni_real_dist** into **48217d2918d64ac84c13822384c67c4edcad5481 on boostorg:develop**.

---

## Comment 2

> Username: coveralls  
> Created_at: 2016-04-24 23:47:12 UTC  
> Url: https://github.com/boostorg/compute/pull/595#issuecomment-214067630  

[![Coverage Status](https://coveralls.io/builds/5912696/badge)](https://coveralls.io/builds/5912696)  
  
Coverage increased (+0.02%) to 88.808% when pulling **5a987feea8080e82a84bedf99944aca625c20386 on haahh:pr_fix_uni_real_dist** into **48217d2918d64ac84c13822384c67c4edcad5481 on boostorg:develop**.

---

## Comment 3

> Username: jszuppe  
> Created_at: 2016-04-25 12:25:28 UTC  
> Url: https://github.com/boostorg/compute/pull/595#issuecomment-214293242  

Travis-CI tests failed only because of `find_end` algorithm. It's fixed by https://github.com/boostorg/compute/pull/597.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2016-04-25 17:37:09 UTC  
> Updated_at: 2016-04-25 17:46:56 UTC  
> Url: https://github.com/boostorg/compute/pull/595#discussion_r60954406  

Shouldn't this be between `0.9` and `1.0`?

---

## Comment 5

> Username: jszuppe  
> Created_at: 2016-04-25 17:40:01 UTC  
> Updated_at: 2016-04-25 17:46:56 UTC  
> Url: https://github.com/boostorg/compute/pull/595#discussion_r60954872  

You're right! I'll fix this in a minute.

---

## Comment 6

> Username: jszuppe  
> Created_at: 2016-04-25 17:47:58 UTC  
> Url: https://github.com/boostorg/compute/pull/595#discussion_r60956201  

Fixed.

---

## Comment 7

> Username: coveralls  
> Created_at: 2016-04-25 20:33:23 UTC  
> Url: https://github.com/boostorg/compute/pull/595#issuecomment-214512012  

[![Coverage Status](https://coveralls.io/builds/5923816/badge)](https://coveralls.io/builds/5923816)  
  
Coverage decreased (-0.4%) to 88.808% when pulling **8aea29032044072080668898ac46bc4393e13cd7 on haahh:pr_fix_uni_real_dist** into **8e5151aec82e6061a58abf600edfb56c1077db5e on boostorg:develop**.

---
