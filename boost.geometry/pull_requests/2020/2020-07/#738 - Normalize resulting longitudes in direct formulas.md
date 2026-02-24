# #738 [formulas] Normalize resulting longitudes in direct formulas. [Merged]

> Username: awulkiew  
> Created at: 2020-07-21 00:41:34 UTC  
> Updated at: 2020-07-31 21:43:29 UTC  
> Merged at: 2020-07-31 21:43:29 UTC  
> Closed at: 2020-07-31 21:43:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/738  

Currently when the point is near antimeridian and the azimuth is pointing out to the other side of it the longitude of the resulting point is out of range, e.g.: `point{179.5, 0}`, `azimuth12{90}` and `distance{111680}` results in longitude around `180.5`.

---

## Comment 1

> Username: vissarion  
> Created_at: 2020-07-21 07:11:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/738#issuecomment-661678734  

Why this is a problem? Is it written somewhere that formulas guarantee (-180,180) range or this normalization could be done (if needed) by a higher level algorithm (that uses the direct formulas)?

---

## Comment 2

> Username: awulkiew  
> Created_at: 2020-07-21 11:58:09 UTC  
> Updated_at: 2020-07-21 12:04:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/738#issuecomment-661811977  

Yes, technically that's correct. But you'd have to do the normalization in all strategies and algorithms using these formulas and remember that they do not return coordinates in correct range. This is error-prone. I'd rather have formulas generating correct coordinates from the start. Besides coordinates are already normalized in Karney's formula. And azimuths are already normalized in inverse formulas.

---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2020-07-21 12:21:29 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/738#pullrequestreview-452388225  

OK, I agree, but since we support this it is useful to state it somewhere (probably in documentation) that the results of the algorithms for non-cartesian CSs are normalized in (-180,180). Also, I am not sure that this holds for all algorithms.

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
 222 |+             // of range. Therefore normalize.
 223 |+             // In other formulas this has to be done at the end because
 224 |+             // otherwise because differential quantities are calculated
```

> Username: vissarion  
> Created_at: 2020-07-21 12:06:37 UTC  
> Updated_at: 2020-07-31 17:44:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/738#discussion_r458045629  

second "because" not needed


📁 include/boost/geometry/formulas/spherical.hpp

```diff
 275 |+             result.lon2 -= 2 * pi;
 276 |+         else if (result.lon2 <= -pi)
 277 |+             result.lon2 += 2 * pi;
```

> Username: vissarion  
> Created_at: 2020-07-21 12:08:11 UTC  
> Updated_at: 2020-07-31 17:44:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/738#discussion_r458046510  

why not using `math::normalize_longitude` as in `karney_direct`?

> Username: awulkiew  
> Created_at: 2020-07-21 12:30:03 UTC  
> Updated_at: 2020-07-31 17:44:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/738#discussion_r458058280  

I was wondering about it but decided to not use this one because it is more lightweight. `math::normalize_longitude` is able to normalize any coordinates no matter how far the value is from the correct range. Here we know that the result will be only slightly off.

> Username: vissarion  
> Created_at: 2020-07-21 12:44:05 UTC  
> Updated_at: 2020-07-31 17:44:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/738#discussion_r458066283  

I see, then if this is potentially useful in other places as well should you consider adding it as a utility?

> Username: awulkiew  
> Created_at: 2020-07-21 15:13:10 UTC  
> Updated_at: 2020-07-31 17:44:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/738#discussion_r458175425  

Yes, that's a good idea. I'll do it.

> Username: awulkiew  
> Created_at: 2020-07-31 17:47:20 UTC  
> Updated_at: 2020-07-31 17:47:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/738#discussion_r463746480  

Done. Please let me know whether or not you're fine with the functions.

> Username: vissarion  
> Created_at: 2020-07-31 19:37:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/738#discussion_r463796222  

Thanks, it's fine!


---

## Comment 4

> Username: awulkiew  
> Created_at: 2020-07-21 17:00:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/738#issuecomment-661983181  

> OK, I agree, but since we support this it is useful to state it somewhere (probably in documentation) that the results of the algorithms for non-cartesian CSs are normalized in (-180,180). Also, I am not sure that this holds for all algorithms.  
  
AFAIK they should be. Theoretically we also require the input coordinates in this range but in practice we normalize input coordinates before processing them. The only exception is `envelope()` which can return max longitude greater than 180 deg because max has to be greater than min.  
  
And yes, it should probably be clearly stated in the documentation. A good place would probably be the documentation for `bg::cs::geographic` and `bg::cs::cartesian`. Or there could be a new/separate section describing the difference between coordinate systems, etc. because the users are sometimes confused. We could do this along with introducing the new umbrella strategies which would be consistent accross coordinate systems.

---
