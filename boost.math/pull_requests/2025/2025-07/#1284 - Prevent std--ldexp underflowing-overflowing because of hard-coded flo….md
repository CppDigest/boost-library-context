# #1284 Prevent std::ldexp underflowing/overflowing because of hard-coded flo… [Merged]

> Username: ak-ambi  
> Created at: 2025-07-03 13:31:48 UTC  
> Updated at: 2025-07-07 17:29:47 UTC  
> Merged at: 2025-07-07 17:29:47 UTC  
> Closed at: 2025-07-07 17:29:47 UTC  
> Url: https://github.com/boostorg/math/pull/1284  

Prevent `std::ldexp` underflowing/overflowing because of hard-coded float type.  
With exceptions disabled, I would expect `errno` to be set only on real issues. However I was hit by errno set to `ERANGE` for following example:  
  
```  
T crossover = ldexp(1.0f, iround(99 / -0.654f));  
```  
  
This means that `float` is not able to fit `crossover` even for `df` as small as 99.

---

## Review 1 [Commented]

> Username: mborland  
> Created_at: 2025-07-03 13:44:19 UTC  
> State: DISMISSED  
> Url: https://github.com/boostorg/math/pull/1284#pullrequestreview-2983259109  

This looks reasonable to me, and I kicked off the CI run. Any different opinion @jzmaddock?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2025-07-03 18:09:13 UTC  
> Url: https://github.com/boostorg/math/pull/1284#issuecomment-3033130429  

I think we need a test case, because to my eyes this doesn't actually change anything?  
  
Note that locally I'm unable to reproduce, but that may be because whether ERANGE is set on ldexp underflow is implementation defined.  Off the top of my head, I think we need something more like:  
  
```  
T twos_power = iround(T(df / -0.654f), typename policies::normalise<Policy, policies::rounding_error<policies::ignore_error> >::type());  
T crossover = twos_power < std::numeric_limits<T>::min_exponent ? 0 : ldexp(1.0f, twos_power);  
```

---

## Comment 3

> Username: ak-ambi  
> Created_at: 2025-07-04 00:11:22 UTC  
> Url: https://github.com/boostorg/math/pull/1284#issuecomment-3033984034  

> I think we need a test case, because to my eyes this doesn't actually change anything?  
  
I'll try to add test case tomorrow. The notable change is that new code does not cast T to float anymore. Double precision was enough to avoid ERANGE.  
  
> Note that locally I'm unable to reproduce, but that may be because whether ERANGE is set on ldexp underflow is implementation defined. Off the top of my head, I think we need something more like:  
>   
> ```  
> T twos_power = iround(T(df / -0.654f), typename policies::normalise<Policy, policies::rounding_error<policies::ignore_error> >::type());  
> T crossover = twos_power < std::numeric_limits<T>::min_exponent ? 0 : ldexp(1.0f, twos_power);  
> ```  
  
it safe to use min_exponent here? I can see some boost support for FLT_RADIX higher than 2, and min_exponent is min exp for `radix` as a base.  
  
Other issue is that limit for T is used, but `float` types are used for `iround` and `ldexp`. Main change from my pull request is that T type is used in all calculations.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2025-07-04 08:00:14 UTC  
> Url: https://github.com/boostorg/math/pull/1284#issuecomment-3034895440  

Ah yes, or just replacing 1.0f with static_cast<T>(1) would do it.  And you're right, my suggestion would fall foul of decimal types, though technically this change just pushes the problem further into the long grass as df > 1021 would still underflow with double.

---

## Comment 5

> Username: ak-ambi  
> Created_at: 2025-07-04 19:14:43 UTC  
> Url: https://github.com/boostorg/math/pull/1284#issuecomment-3037093073  

Ok, I think I found the way to make it more resilient using `frexp` instead of `ldexp`. No threat of underflow or overflow. Please recheck.  
  
Also I have added the test case that reproduced the problem on my environment. I am using `clang` compiler.

---

## Review 6 [Approved]

> Username: mborland  
> Created_at: 2025-07-07 17:28:47 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/1284#pullrequestreview-2994778959  

LGTM. Failures in the drone CI are unrelated

---
