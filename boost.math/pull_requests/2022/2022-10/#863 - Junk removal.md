# #863 Junk removal [Merged]

> Username: SiliconA-Z  
> Created at: 2022-10-30 16:27:03 UTC  
> Updated at: 2022-11-10 18:50:57 UTC  
> Merged at: 2022-11-10 18:45:07 UTC  
> Closed at: 2022-11-10 18:45:07 UTC  
> Url: https://github.com/boostorg/math/pull/863  

Removal of junk headers, typos, or mistakenly duplicated keywords

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2022-10-30 17:06:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/863#pullrequestreview-1161165811  

📁 example/color_maps_sf_example.cpp

```diff
 582 |        for (int64_t i = image_width / 2; i < image_width; ++i)
 583 |-           points[image_width * (image_height - 1) + i] = i & 1 ? 1 : 0;
 583 |+           points[image_width * (image_height - 1) + i] = i & 1;
```

> Username: jzmaddock  
> Created_at: 2022-10-30 17:06:46 UTC  
> Url: https://github.com/boostorg/math/pull/863#discussion_r1008896789  

This is a superfluous change IMO.

> Username: SiliconA-Z  
> Created_at: 2022-10-30 17:11:34 UTC  
> Url: https://github.com/boostorg/math/pull/863#discussion_r1008897382  

The expression is superfluous to start with

> Username: jzmaddock  
> Created_at: 2022-10-30 17:19:12 UTC  
> Url: https://github.com/boostorg/math/pull/863#discussion_r1008898239  

How so?

> Username: SiliconA-Z  
> Created_at: 2022-10-30 17:21:17 UTC  
> Url: https://github.com/boostorg/math/pull/863#discussion_r1008898467  

i & 1 can only evaluate to 1 or 0, so the ternary operator is redundant

> Username: jzmaddock  
> Created_at: 2022-10-30 17:23:45 UTC  
> Url: https://github.com/boostorg/math/pull/863#discussion_r1008898747  

Doh! My apologies I read the diff backwards and thought you were adding the ?: operator.  
  
My bad again!


---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2022-10-30 17:21:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/863#pullrequestreview-1161167294  

📁 include/boost/math/distributions/poisson.hpp

```diff
 555 | // keep compilers that support two-phase lookup happy.
 556 | #include <boost/math/distributions/detail/derived_accessors.hpp>
 557 |- #include <boost/math/distributions/detail/inv_discrete_quantile.hpp>
```

> Username: jzmaddock  
> Created_at: 2022-10-30 17:21:28 UTC  
> Url: https://github.com/boostorg/math/pull/863#discussion_r1008898486  

There is a genuine mistake here, based on the comment in the file, it looks like we should be removing the *first* rather than last occurrence of the #include.  My bad on this one!

> Username: SiliconA-Z  
> Created_at: 2022-10-30 17:45:44 UTC  
> Url: https://github.com/boostorg/math/pull/863#discussion_r1008901653  

Ok so I removed that instead and now it doesn't build


---

## Review 3 [Commented]

> Username: jzmaddock  
> Created_at: 2022-10-30 17:22:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/863#pullrequestreview-1161167352  

📁 include/boost/math/interpolators/detail/cardinal_cubic_b_spline_detail.hpp

```diff
 172 |     // Since the splines have compact support, they decay to zero very fast outside the endpoints.
 173 |-     // This is often very annoying; we'd like to evaluate the interpolant a little bit outside the
 173 |+     // This is often very annoying; we'd like to evaluate the interpolant a little outside the
```

> Username: jzmaddock  
> Created_at: 2022-10-30 17:22:09 UTC  
> Updated_at: 2022-10-30 17:22:10 UTC  
> Url: https://github.com/boostorg/math/pull/863#discussion_r1008898583  

We could argue this one forever, but the original text makes perfect sense to me.


---

## Comment 4

> Username: SiliconA-Z  
> Created_at: 2022-10-30 17:25:09 UTC  
> Url: https://github.com/boostorg/math/pull/863#issuecomment-1296308198  

Done @jzmaddock

---

## Review 5 [Commented]

> Username: NAThompson  
> Created_at: 2022-10-31 04:15:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/863#pullrequestreview-1161307794  

📁 include/boost/math/special_functions/nonfinite_num_facets.hpp

```diff
 224 |       }
 225 | 
 226 |-     private:
```

> Username: NAThompson  
> Created_at: 2022-10-31 04:15:04 UTC  
> Url: https://github.com/boostorg/math/pull/863#discussion_r1009015936  

I'm confused; why remove the `private` keyword here?

> Username: SiliconA-Z  
> Created_at: 2022-10-31 13:15:38 UTC  
> Url: https://github.com/boostorg/math/pull/863#discussion_r1009406481  

Ditto as below

> Username: SiliconA-Z  
> Created_at: 2022-10-31 13:15:58 UTC  
> Updated_at: 2022-10-31 13:16:07 UTC  
> Url: https://github.com/boostorg/math/pull/863#discussion_r1009406800  

It is still private. Just that the private keyword is already above


---

## Review 6 [Commented]

> Username: NAThompson  
> Created_at: 2022-10-31 04:16:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/863#pullrequestreview-1161308208  

📁 include/boost/math/quadrature/detail/sinh_sinh_detail.hpp

```diff
  51 | 
  52 | private:
  53 |- private:
```

> Username: NAThompson  
> Created_at: 2022-10-31 04:16:04 UTC  
> Updated_at: 2022-10-31 04:16:05 UTC  
> Url: https://github.com/boostorg/math/pull/863#discussion_r1009016227  

Why remove it? The `get_abscissa_row` function below is not part of the public API.

> Username: SiliconA-Z  
> Created_at: 2022-10-31 13:15:28 UTC  
> Url: https://github.com/boostorg/math/pull/863#discussion_r1009406325  

Because private is literally above it. It is a duplicate

> Username: NAThompson  
> Created_at: 2022-10-31 14:15:53 UTC  
> Updated_at: 2022-10-31 14:15:54 UTC  
> Url: https://github.com/boostorg/math/pull/863#discussion_r1009470408  

yup; my bad.


---

## Comment 7

> Username: SiliconA-Z  
> Created_at: 2022-10-31 13:31:47 UTC  
> Url: https://github.com/boostorg/math/pull/863#issuecomment-1297096942  

@mborland Ready!

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2022-11-04 17:05:47 UTC  
> Url: https://github.com/boostorg/math/pull/863#issuecomment-1303891658  

@AtariDreams : other than my 2 comments above, I'm happy with this one (but I think those two need to be addressed).

---

## Comment 9

> Username: SiliconA-Z  
> Created_at: 2022-11-04 17:07:13 UTC  
> Url: https://github.com/boostorg/math/pull/863#issuecomment-1303893166  

#include <boost/math/distributions/detail/inv_discrete_quantile.hpp> was removed and the project failed to build as a result, so I think we need to keep it as is

---

## Comment 10

> Username: SiliconA-Z  
> Created_at: 2022-11-04 17:09:02 UTC  
> Url: https://github.com/boostorg/math/pull/863#issuecomment-1303895123  

Also the first complaint of yours was addressed @jzmaddock

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2022-11-04 17:43:16 UTC  
> Url: https://github.com/boostorg/math/pull/863#issuecomment-1303937452  

There are two issues mentioned above: the workaround removed from tools/config.hpp properly belongs in tools/workaround.hpp (and yes we really do need float.h and not cfloat when the workaround is active), and the change to tools/precision.hpp will introduce a new compiler warning as it implicitly converts int to bool.

---

## Comment 12

> Username: SiliconA-Z  
> Created_at: 2022-11-05 16:14:31 UTC  
> Url: https://github.com/boostorg/math/pull/863#issuecomment-1304575798  

Done! @jzmaddock

---

## Comment 13

> Username: SiliconA-Z  
> Created_at: 2022-11-09 20:49:21 UTC  
> Url: https://github.com/boostorg/math/pull/863#issuecomment-1309358528  

@mborland All ready!

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2022-11-10 18:50:56 UTC  
> Url: https://github.com/boostorg/math/pull/863#issuecomment-1310753141  

Ooops, merged too soon: "constructible" is the correct spelling as least as far as the C++ standard is concerned.  Changed that back in develop.

---
