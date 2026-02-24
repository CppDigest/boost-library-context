# #977 Fix for scipy issue 18302 [Merged]

> Username: mborland  
> Created at: 2023-04-14 14:58:43 UTC  
> Updated at: 2023-04-21 10:31:28 UTC  
> Merged at: 2023-04-21 10:31:24 UTC  
> Closed at: 2023-04-21 10:31:24 UTC  
> Url: https://github.com/boostorg/math/pull/977  

See: https://github.com/scipy/scipy/issues/18302  
  
Adds bounds checking so we don't return a pdf larger than beta, and catches an overflow exception when x is near `std::numeric_limits::min`.

---

## Comment 1

> Username: WarrenWeckesser  
> Created_at: 2023-04-14 16:49:22 UTC  
> Url: https://github.com/boostorg/math/pull/977#issuecomment-1508947954  

Thanks for the fast response, @mborland!  
  
It looks like the code in `test/scipy_issue_18302.cpp` uses the default policy--is that correct?  I wrote a short C++ code to check the case `x = 1e-310`, and I didn't get the overflow error with the default policy. I had to use `promote_double<false>`.

---

## Comment 2

> Username: mborland  
> Created_at: 2023-04-14 17:09:32 UTC  
> Url: https://github.com/boostorg/math/pull/977#issuecomment-1508975813  

> Thanks for the fast response, @mborland!  
>   
>   
>   
> It looks like the code in `test/scipy_issue_18302.cpp` uses the default policy--is that correct?  I wrote a short C++ code to check the case `x = 1e-310`, and I didn't get the overflow error with the default policy. I had to use `promote_double<false>`.  
>   
>   
  
I did get overflow with the default policy. Now that you bring it up I ran it on an ARM Mac which means long double = double. I think in this case it's worth effectively ignoring the overflow exception because we know what the result is supposed to be when that happens.

---

## Comment 3

> Username: WarrenWeckesser  
> Created_at: 2023-04-14 17:29:19 UTC  
> Url: https://github.com/boostorg/math/pull/977#issuecomment-1508997130  

Ah, I see.  I tested on a Linux machine, where `long double` is extended precision.  I was just worried that the test wasn't actually testing the fix because it would pass even without the fix.  But that is not the case on platforms where `long double` is `double`.

---

## Comment 4

> Username: mborland  
> Created_at: 2023-04-14 17:37:14 UTC  
> Url: https://github.com/boostorg/math/pull/977#issuecomment-1509005189  

> Ah, I see.  I tested on a Linux machine, where `long double` is extended precision.  I was just worried that the test wasn't actually testing the fix because it would pass even without the fix.  But that is not the case on platforms where `long double` is `double`.  
  
We recently added ARM and s390x to the CI system so we have 64, 80, and 128 bit long doubles are all tested now. Its helped find overflows and precision issues.

---

## Comment 5

> Username: WarrenWeckesser  
> Created_at: 2023-04-14 17:44:43 UTC  
> Url: https://github.com/boostorg/math/pull/977#issuecomment-1509011891  

Nice.  I think it might be slightly better to use `promote_double<false>` so the test in fact fails on all platforms without the fix, and therefore the test is verifying that the change actually fixes the problem on all platforms, but obviously that's your call.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2023-04-14 17:58:57 UTC  
> Url: https://github.com/boostorg/math/pull/977#issuecomment-1509028202  

@mborland I think we should fix this updatream in ibeta_derivative here: https://github.com/boostorg/math/blob/109a814e89f77ff8a3fc8f0391f6b35a12640669/include/boost/math/special_functions/beta.hpp#L1455  It's the 1/y that fails and we need to decide whether to return the derivative at zero (which may well be infinite) or at `tools::min_value<T>()` which may well not be.  I'm edging towards the latter as the easier fix.

---

## Comment 7

> Username: mborland  
> Created_at: 2023-04-18 10:39:46 UTC  
> Url: https://github.com/boostorg/math/pull/977#issuecomment-1512850926  

@jzmaddock This is green and I think hits your comments from both threads.

---

## Review 8 [Commented]

> Username: jzmaddock  
> Created_at: 2023-04-18 18:00:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/977#pullrequestreview-1390668346  

📁 include/boost/math/distributions/beta.hpp

```diff
 428 |+       if (return_val > b)
 429 |+       {
 430 |+         return_val = b;
```

> Username: jzmaddock  
> Created_at: 2023-04-18 18:00:41 UTC  
> Updated_at: 2023-04-18 18:00:42 UTC  
> Url: https://github.com/boostorg/math/pull/977#discussion_r1170397215  

This doesn't look right to me - surely the PDF can be larger than the shape parameter?  Indeed can it not go to infinity at the endpoints?

> Username: mborland  
> Created_at: 2023-04-19 08:54:33 UTC  
> Updated_at: 2023-04-19 08:54:34 UTC  
> Url: https://github.com/boostorg/math/pull/977#discussion_r1171030800  

You're right. I was trying to catch the instance where we overshoot the endpoint (like in the linked issue). I can't think of any particularly good ways to do so.

> Username: mborland  
> Created_at: 2023-04-19 11:55:36 UTC  
> Url: https://github.com/boostorg/math/pull/977#discussion_r1171229790  

`test_beta_dist` uses 100 eps as the tolerance in the spot checks. I get under 40 eps above the endpoint from the linked issue locally.

> Username: jzmaddock  
> Created_at: 2023-04-19 12:15:13 UTC  
> Url: https://github.com/boostorg/math/pull/977#discussion_r1171250177  

Nod.  The only endpoint-overshoot that can occur is when one of alpha and beta is exactly one, and then the endpoint is `1 / beta(alpha, beta)` and we currently return values very close to but either side of that.  Whether that's actually an issue I'm not sure.


---

## Comment 9

> Username: mborland  
> Created_at: 2023-04-20 10:51:27 UTC  
> Url: https://github.com/boostorg/math/pull/977#issuecomment-1516118105  

@jzmaddock Any other changes for this one, or good to merge?

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2023-04-20 15:30:56 UTC  
> Url: https://github.com/boostorg/math/pull/977#issuecomment-1516539578  

Looks good to me!

---
