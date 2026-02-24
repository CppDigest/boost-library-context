# #1074 Fix special case of area for segments that pass through a pole. [Merged]

> Username: vissarion  
> Created at: 2022-10-21 14:42:13 UTC  
> Updated at: 2022-11-16 13:05:24 UTC  
> Merged at: 2022-11-16 13:05:20 UTC  
> Closed at: 2022-11-16 13:05:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1074  

This PR proposes a fix for https://github.com/boostorg/geometry/issues/1063

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2022-10-21 15:36:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1074#pullrequestreview-1151170356  

📁 include/boost/geometry/formulas/area_formulas.hpp

```diff
 373 |+         CT const lat2r = get_as_radian<1>(p2);
 374 |+ 
 375 |+         CT lon12r = lon1r - lon2r;
```

> Username: awulkiew  
> Created_at: 2022-10-21 15:36:02 UTC  
> Updated_at: 2022-10-21 15:36:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1074#discussion_r1001932086  

I'm aware that this naming was used before but I'm curious about the convention. Shouldn't this variable be called `lon21r` since it's an angle from `lon2r` to `lon1r`?  
  
Azimuth between points of coordinates `lon1, lat1` and `lon2, lat2` is called `a12` in the code. Other variables referred to the azimuth e.g. in direct and inverse formulas are also called this way. Basically the order of numbers defines the direction.  
  
Here the order of numbers defines the subtraction. This is also fine but I'm wondering whether or not it may be confusing.

> Username: vissarion  
> Created_at: 2022-10-21 16:18:20 UTC  
> Updated_at: 2022-10-21 16:18:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1074#discussion_r1001972362  

Good point! The wikipedia https://en.wikipedia.org/wiki/Geodesics_on_an_ellipsoid is also using what you propose (i.e. $\lambda_{12}=\lambda_2-\lambda_1$)  
In my case since only the absolute value matters I change the order of the subtraction (following the convention you propose). That also simplifies the call to `trapezoidal_formula()` below.


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2022-10-21 15:37:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1074#pullrequestreview-1151175186  

📁 include/boost/geometry/formulas/area_formulas.hpp

```diff
 597 |+ 
 598 |+         CT p1_lon = lon1r - ( floor( lon1r / two_pi ) * two_pi );
 599 |+         CT p2_lon = lon2r - ( floor( lon2r / two_pi ) * two_pi );
```

> Username: awulkiew  
> Created_at: 2022-10-21 15:37:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/1074#discussion_r1001935498  

`math::floor` or `std::floor`?

> Username: vissarion  
> Created_at: 2022-10-21 15:55:27 UTC  
> Updated_at: 2022-10-21 15:55:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1074#discussion_r1001952345  

aah that was there already, but you are right, I should be more specific. `std::floor` should be better.


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2022-10-21 22:42:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1074#pullrequestreview-1151613988  

📁 include/boost/geometry/formulas/area_formulas.hpp

```diff
 588 |+         CT const lon2r = get_as_radian<0>(p2);
 589 |+ 
 590 |+         CT lon12 = lon1r - lon2r;
```

> Username: awulkiew  
> Created_at: 2022-10-21 22:42:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/1074#discussion_r1002228575  

Here is the last one. :)


---

## Review 4 [Approved]

> Username: barendgehrels  
> Created_at: 2022-11-16 09:41:41 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1074#pullrequestreview-1182212817  

Thanks! Skipping the math details in the review

📁 include/boost/geometry/formulas/area_formulas.hpp

```diff
 348 |+         CT const c2 = CT(2);
 349 |+         CT tan_lat1 = tan(lat1r / c2);
 350 |+         CT tan_lat2 = tan(lat2r / c2);
```

> Username: barendgehrels  
> Created_at: 2022-11-16 09:35:08 UTC  
> Updated_at: 2022-11-16 09:41:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1074#discussion_r1023744674  

Can be `const` as well

---

📁 include/boost/geometry/formulas/area_formulas.hpp

```diff
 478 |-             || lat2r == half_pi || lat2r == -half_pi;
 490 |+         CT lon12r = lon2r - lon1r;
 491 |+         math::normalize_longitude<radian, CT>(lon12r);
```

> Username: barendgehrels  
> Created_at: 2022-11-16 09:37:13 UTC  
> Updated_at: 2022-11-16 09:41:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1074#discussion_r1023747573  

-> `const`

> Username: vissarion  
> Created_at: 2022-11-16 11:10:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/1074#discussion_r1023857422  

`lon12r` cannot be `const` since `normalize_longitude` is changing it.

> Username: barendgehrels  
> Created_at: 2022-11-16 11:39:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1074#discussion_r1023885304  

All right, missed that, thanks

---

📁 include/boost/geometry/formulas/area_formulas.hpp

```diff
 480 |-         if (!meridian && (i_res.distance)
 481 |-             < mean_radius<CT>(spheroid_const.m_spheroid) / CT(638))  // short segment
 493 |+         if (lon12r == pi || lon12r == -pi)
```

> Username: barendgehrels  
> Created_at: 2022-11-16 09:37:47 UTC  
> Updated_at: 2022-11-16 09:41:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1074#discussion_r1023748474  

Is a double comparison `==` OK in this case?  
Probably, but maybe you can add a comment

> Username: vissarion  
> Created_at: 2022-11-16 11:17:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/1074#discussion_r1023863231  

OK I added a comment

---

📁 include/boost/geometry/formulas/area_formulas.hpp

```diff
 503 |- 
 504 |-             excess = alp2 - alp1;
 499 |+             bool meridian = lon12r == c0
```

> Username: barendgehrels  
> Created_at: 2022-11-16 09:37:57 UTC  
> Updated_at: 2022-11-16 09:41:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1074#discussion_r1023748781  

-> `const`

---

📁 include/boost/geometry/formulas/area_formulas.hpp

```diff
 591 |+         math::normalize_longitude<radian, CT>(lon12);
 592 |+ 
 593 |+         if (lon12 == pi || lon12 == -pi)
```

> Username: barendgehrels  
> Created_at: 2022-11-16 09:38:49 UTC  
> Updated_at: 2022-11-16 09:41:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1074#discussion_r1023750347  

Same here, `==` OK?

> Username: vissarion  
> Created_at: 2022-11-16 11:24:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1074#discussion_r1023870615  

yes, I added a similar comment

---

📁 include/boost/geometry/formulas/area_formulas.hpp

```diff
 588 |+         CT const lon2r = get_as_radian<0>(p2);
 589 |+ 
 590 |+         CT lon12 = lon2r - lon1r;
```

> Username: barendgehrels  
> Created_at: 2022-11-16 09:39:24 UTC  
> Updated_at: 2022-11-16 09:41:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1074#discussion_r1023751425  

-> `const`

> Username: vissarion  
> Created_at: 2022-11-16 11:21:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1074#discussion_r1023867008  

cannot declare `const` here (see comment above)

---

📁 include/boost/geometry/formulas/area_formulas.hpp

```diff
 597 |+ 
 598 |+         CT p1_lon = lon1r - ( std::floor( lon1r / two_pi ) * two_pi );
 599 |+         CT p2_lon = lon2r - ( std::floor( lon2r / two_pi ) * two_pi );
```

> Username: barendgehrels  
> Created_at: 2022-11-16 09:39:34 UTC  
> Updated_at: 2022-11-16 09:41:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1074#discussion_r1023751759  

-> `const`

---

📁 include/boost/geometry/formulas/area_formulas.hpp

```diff
 395 |+             CT const sbet1_cbet2 = sbet1 * cbet2;
 396 |+             CT alp1 = atan2(cbet1_sbet2 - sbet1_cbet2 * comg12, cbet2 * somg12);
 397 |+             CT alp2 = atan2(cbet1_sbet2 * comg12 - sbet1_cbet2, cbet1 * somg12);
```

> Username: barendgehrels  
> Created_at: 2022-11-16 09:40:42 UTC  
> Updated_at: 2022-11-16 09:41:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1074#discussion_r1023754273  

-> `const` most of the constants above


---

## Comment 5

> Username: vissarion  
> Created_at: 2022-11-16 11:25:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1074#issuecomment-1316842131  

@barendgehrels thanks for the review comments, I replied to all and pushed two new commits for addressing them.

---
