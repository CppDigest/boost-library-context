# #417 Improve ulps plot: [Merged]

> Username: jzmaddock  
> Created at: 2020-08-09 10:36:56 UTC  
> Updated at: 2020-08-11 15:48:05 UTC  
> Merged at: 2020-08-11 15:44:33 UTC  
> Closed at: 2020-08-11 15:44:33 UTC  
> Url: https://github.com/boostorg/math/pull/417  

Use numeric_limits for precision comparison, plot out of bounds points at the clip boundary so we can still see that they're there.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2020-08-09 10:42:47 UTC  
> Url: https://github.com/boostorg/math/pull/417#issuecomment-671035932  

@NAThompson : A quick question:  
  
* What should we do with errors that are NaN's?  Don't these reflect a possible bug in the function?  
  
Other than that this fixes the case where PreciseReal is a multiprecision type.

---

## Review 2 [Commented]

> Username: NAThompson  
> Created_at: 2020-08-09 13:47:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/417#pullrequestreview-463875704  

📁 include/boost/math/tools/ulps_plot.hpp

```diff
 438 |-     static_assert(sizeof(PreciseReal) >= sizeof(CoarseReal), "PreciseReal must have larger size than CoarseReal");
 441 |+     // Use digits10 for this comparison in case the two types have differeing radixes:
 442 |+     static_assert(std::numeric_limits<PreciseReal>::digits10 >= std::numeric_limits<CoarseReal>::digits10, "PreciseReal must have higher precision that CoarseReal");
```

> Username: NAThompson  
> Created_at: 2020-08-09 13:47:49 UTC  
> Updated_at: 2020-08-10 17:22:55 UTC  
> Url: https://github.com/boostorg/math/pull/417#discussion_r467586009  

Yes, I've noticed this problem before for MPFR types; since the `sizeof` operator returns the size of a pointer. Thanks!


---

## Review 3 [Commented]

> Username: NAThompson  
> Created_at: 2020-08-09 13:48:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/417#pullrequestreview-463875736  

📁 include/boost/math/tools/ulps_plot.hpp

```diff
 250 |+                    CoarseReal x = x_scale(plot.coarse_abscissas_[j]);
 251 |+                    PreciseReal y = y_scale(static_cast<PreciseReal>(ulp[j] < 0 ? -plot.clip_ : plot.clip_));
 252 |+                    fs << "<circle cx='" << x << "' cy='" << y << "' r='1' fill='" << color << "'/>\n";
```

> Username: NAThompson  
> Created_at: 2020-08-09 13:48:28 UTC  
> Updated_at: 2020-08-10 17:22:55 UTC  
> Url: https://github.com/boostorg/math/pull/417#discussion_r467586055  

Should we maybe color the clipped values differently? Suppose red for all clips?

> Username: NAThompson  
> Created_at: 2020-08-09 14:05:24 UTC  
> Updated_at: 2020-08-10 17:22:55 UTC  
> Url: https://github.com/boostorg/math/pull/417#discussion_r467587776  

One further point: If the condition number goes infinite somewhere, which is very common, the top of the plot is going to be a bloodbath, even if there's really nothing wrong with the implementation.  
  
I'm not sure what to do about this, since it seems like something no one wants an option for!

> Username: jzmaddock  
> Created_at: 2020-08-09 19:10:57 UTC  
> Updated_at: 2020-08-10 17:22:55 UTC  
> Url: https://github.com/boostorg/math/pull/417#discussion_r467618081  

+1 on red for clipped values, and yes the top of the plot may be a bloodbath, but my feeling is it's better to know than not about this stuff.


---

## Comment 4

> Username: NAThompson  
> Created_at: 2020-08-09 13:53:24 UTC  
> Url: https://github.com/boostorg/math/pull/417#issuecomment-671054730  

> What should we do with errors that are NaN's? Don't these reflect a possible bug in the function?  
  
Yeah, but it could be a natural value to return at both precisions; e.g. sin(1/x) at x = 0. In this case there is no problem with the ulps plot, you just don't plot that value.  
  
So the only problem is when only one of the two precisions is a nan. In this case, maybe put it at the clip? But if there is no clip specified, then I would probably do nothing; or a message to `std::cerr`. Graphical techniques can't be expected to ergonomically represent all classes of errors.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2020-08-09 19:13:00 UTC  
> Url: https://github.com/boostorg/math/pull/417#issuecomment-671090028  

OK yes, I see that we need to support NaN's when the function goes out of domain or whatever.  It's tough to do the right thing in all cases here!

---

## Comment 6

> Username: cosurgi  
> Created_at: 2020-08-10 17:27:34 UTC  
> Updated_at: 2020-08-10 17:28:42 UTC  
> Url: https://github.com/boostorg/math/pull/417#issuecomment-671486324  

> function goes out of domain  
  
actually a helper function which returns `true`, `false` about whether given `x` is in a function domain would be awesome.  
  
Then I wouldn't make that one lgamma mistake in https://github.com/boostorg/multiprecision/issues/264 :-))

---

## Comment 7

> Username: NAThompson  
> Created_at: 2020-08-11 14:36:18 UTC  
> Url: https://github.com/boostorg/math/pull/417#issuecomment-671984981  

@jzmaddock : I like these diffs; should I squash merge them?

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2020-08-11 15:36:42 UTC  
> Url: https://github.com/boostorg/math/pull/417#issuecomment-672022050  

>I like these diffs; should I squash merge them?  
  
Yes please do, thanks!

---
