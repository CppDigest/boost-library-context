# #624 Fixes for compile tests [Merged]

> Username: mborland  
> Created at: 2021-05-06 16:12:15 UTC  
> Updated at: 2021-06-27 16:16:47 UTC  
> Merged at: 2021-05-21 07:47:43 UTC  
> Closed at: 2021-05-21 07:47:43 UTC  
> Url: https://github.com/boostorg/math/pull/624  

Fixes for failures from -Wzero-length-array (see #614 discussion) and other warnings.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-05-06 16:47:55 UTC  
> Url: https://github.com/boostorg/math/pull/624#issuecomment-833679588  

All the diffs I understand I approve of!

---

## Comment 2

> Username: mborland  
> Created_at: 2021-05-07 15:11:37 UTC  
> Url: https://github.com/boostorg/math/pull/624#issuecomment-834500647  

This should be good to go. CI is clean

---

## Review 3 [Commented]

> Username: jzmaddock  
> Created_at: 2021-05-07 17:06:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/624#pullrequestreview-654693236  

📁 include/boost/math/special_functions/rsqrt.hpp

```diff
  17 |     using std::sqrt;
  18 |-     if constexpr (std::is_same_v<Real, float> || std::is_same_v<Real, double> || std::is_same_v<Real, long double>)
  18 |+     if constexpr (std::is_floating_point_v<Real>)
```

> Username: jzmaddock  
> Created_at: 2021-05-07 17:06:09 UTC  
> Updated_at: 2021-05-11 19:43:07 UTC  
> Url: https://github.com/boostorg/math/pull/624#discussion_r628377580  

@NAThompson : was the original intention here to exclude `__float128`?  If so replacing with is_floating_point *might* fail in -std=gnu++ mode.   Just double checking...

> Username: NAThompson  
> Created_at: 2021-05-11 12:14:34 UTC  
> Updated_at: 2021-05-11 19:43:07 UTC  
> Url: https://github.com/boostorg/math/pull/624#discussion_r630110192  

Amazingly enough I remember this! Yes, that was the goal.  
  
As you can see in the `else` branch, the `rsqrt` iteration starts with the `long double` approximation and then refines it with the Newton method for `1/sqrt(x)`. I verified that it is slower to call `1/sqrt(x)` for `float128` than to use the `long double` approximation and Newton iterate.

> Username: mborland  
> Created_at: 2021-05-11 19:44:04 UTC  
> Url: https://github.com/boostorg/math/pull/624#discussion_r630472011  

This change has been reverted in 2a5bbf3


---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2021-05-07 17:10:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/624#pullrequestreview-654696449  

📁 include/boost/math/special_functions/nonfinite_num_facets.hpp

```diff
 416 |         case 's':
 417 |-           if(flags_ && legacy)
 417 |+           if(flags_)
```

> Username: jzmaddock  
> Created_at: 2021-05-07 17:10:46 UTC  
> Updated_at: 2021-05-11 19:43:07 UTC  
> Url: https://github.com/boostorg/math/pull/624#discussion_r628379969  

Looking at the rest of the code, I assume this should be `flags_&legacy` ?  @pabristow ?

> Username: pabristow  
> Created_at: 2021-05-08 09:23:27 UTC  
> Updated_at: 2021-05-11 19:43:07 UTC  
> Url: https://github.com/boostorg/math/pull/624#discussion_r628726788  

I deny writing it! Looks reasonable, though the comments seem not very specific to me.  I can't see any reason to restrict to only legacy here. And the tests are thin.  And testing is difficult on account of the too many possible floating-point types and platforms.  
  
Lesson to Standards Writers - make up your mind and don't duck out by making stuff implementation-dependent!


---

## Comment 5

> Username: mborland  
> Created_at: 2021-05-11 19:56:41 UTC  
> Url: https://github.com/boostorg/math/pull/624#issuecomment-839080861  

The one erroneous diff has been reverted so should be good to go pending further review.

---
