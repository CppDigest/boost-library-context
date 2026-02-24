# #409 Standalone fixes [Merged]

> Username: mborland  
> Created at: 2022-01-02 12:37:35 UTC  
> Updated at: 2022-01-13 20:36:44 UTC  
> Merged at: 2022-01-10 17:02:18 UTC  
> Closed at: 2022-01-10 17:02:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/409  

The diff stat on this one will be ugly because it reverts @ckormanyos reverting the merge of PR #403. Fixes the review comments from @jzmaddock that led to the revert:  
  
1. Fixes for Eigen types needing the use of `boost::math::tools` instead of `std::numeric_limits` functions.  
2. Replace the use of `std::free` with `gmp::free_function` for better interoperability.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-01-03 09:35:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/409#issuecomment-1003969167  

Just a heads up that this PR https://github.com/boostorg/multiprecision/pull/408 contains fixes that will likely clash with this: in particular all the lexical-based conversions in tommath.hpp are removed in favour of "proper" conversion operators.

---

## Comment 2

> Username: mborland  
> Created_at: 2022-01-03 10:05:19 UTC  
> Updated_at: 2022-01-03 10:06:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/409#issuecomment-1003984409  

Merged in #408 and went with all of your tommath changes in merge conflicts. Good news is there were not many. Once your PR is merged to develop I can rebase to clean this up.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2022-01-06 18:27:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/409#issuecomment-1006817485  

>Merged in #408 and went with all of your tommath changes in merge conflicts. Good news is there were not many. Once your PR is merged to develop I can rebase to clean this up.  
  
#408 is merged now, can you rebase so we can see a cleaner change set?  
  
Many thanks for persevering with this!

---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2022-01-08 16:41:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/409#pullrequestreview-847170826  

📁 include/boost/multiprecision/gmp.hpp

```diff
3807 |       {
3739 |-          return boost::math::tools::epsilon<Real>();
3808 |+          return std::numeric_limits<Real>::epsilon();
```

> Username: jzmaddock  
> Created_at: 2022-01-08 16:41:04 UTC  
> Updated_at: 2022-01-08 16:41:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/409#discussion_r780684585  

This still doesn't look quite right to me: if that PP condition is met, then Boost.Math is available, and we can (and should) be using the Boost.Math functions?

> Username: jzmaddock  
> Created_at: 2022-01-08 16:57:09 UTC  
> Updated_at: 2022-01-08 16:57:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/409#discussion_r780685822  

On second thoughts.... this is a traits-class specialization for gmp_float<0> only, and all the boost math functions except epsilon are defined right here in this file, and epsilon is just `ldexp(Real(1), 1 - digits())`, so the #if logic can go, and we can call the boost::math functions which are already there and do the right thing?


---

## Comment 5

> Username: jzmaddock  
> Created_at: 2022-01-08 17:01:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/409#issuecomment-1008063567  

Other than the comments above, this all looks good to me - many many thanks for this Matt - much appreciated!

---

## Comment 6

> Username: mborland  
> Created_at: 2022-01-09 19:14:14 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/409#issuecomment-1008357284  

I think this is good to go now. The drone run is hung; some of the test have been "running" for over 9 hours.

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2022-01-09 19:56:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/409#issuecomment-1008363262  

> I think this is good to go now.  
  
This is really great. Thank you Matt! Thanks for also guiding this endeavor, John.  
  
@jzmaddock I'm very, very happy with standalone and will leave this merge to you, just in case you've got something else going on.  
  
When the dust settles, I might swing in for some trivial embedded bare-metal tweaks on high-level warnings and string-streaming, but those can wait indefinitely.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2022-01-10 17:02:11 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/409#issuecomment-1009132778  

Looks good, merging...

---
