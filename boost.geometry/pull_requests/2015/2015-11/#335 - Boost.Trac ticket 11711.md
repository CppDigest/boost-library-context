# #335 Boost.Trac ticket 11711 [Merged]

> Username: mkaravel  
> Created at: 2015-11-06 11:05:22 UTC  
> Updated at: 2015-11-26 21:30:58 UTC  
> Merged at: 2015-11-26 21:30:58 UTC  
> Closed at: 2015-11-26 21:30:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/335  

This PR partially addresses Boost.Trac ticket [11711](https://svn.boost.org/trac/boost/ticket/11711): "is_simple and is_valid - Points that are NaN or Infinity".  
  
**Problem:** The problem with the existing `bg::is_valid()` implementation is that it may not handle properly geometries that have invalid floating-point coordinates, namely coordinates that are NaN or infinite. For such geometries `bg::is_valid()` may return `true`, which should not be the case.  
  
**Fix:** In this PR the coordinates of a geometry whose coordinate type is a fundamental floating-point number are checked. If at least one coordinate is found to be NaN or infinite, the geometry is considered as invalid.  
Note that for non-fundamental number types or fundamental non-floating-point types the check is essentially deactivated: fundamental non-floating-point types cannot have NaN or infinite values, whereas for non-fundamental number types a mechanism similar to `std::numeric_limits<>` needs to be present to perform similar checks (and such a mechanism is not present currently).  
  
**is_simple:** The Boost.Trac ticket asks for `bg::is_simple()` to return `false` for geometries that have invalid coordinates. This is not implemented in this PR. As it is the case with almost all BG algorithms (the only exception is `bg::is_valid()`) the geometric input is assumed to be valid, and validity of the input is not checked internally in the algorithm. According to this rule, `bg::is_simple()` should not check for invalid coordinates as suggested in the Boost.Trac ticket; when called with an invalid geometry as input (as it is the case in the ticket) the behavior of `bg::is_simple()` is simply undefined.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-11-16 12:57:30 UTC  
> Updated_at: 2015-11-18 08:48:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/335#discussion_r44919892  

If `isfinite()` was used here instead, internally only 2 conditions would have to be checked per coordinate (`x >= min` and `x <= max`). With `isnan()` and `isinf()` more operations have to be performed (at least 3 condition checks and additional bitwise operations for ieee floats). Would it have sense to replace this?

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-11-18 08:48:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/335#discussion_r45172971  

Done. See commit 348b7f7.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2015-11-19 18:49:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/335#issuecomment-158153373  

Ok, thanks. I'm only wondering if instead of `has_non_finite_coordinate` the name `has_finite_coordinates` would be a better pick. It's because it would be a direct reference to the C++11 standard and Boost.Math `isfinite`. Besides that, I'm ok with merging.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-11-19 19:53:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/335#issuecomment-158175709  

@awulkiew It was the easiest modification to do. Implementing `has_finite_coordinates` would imply quite a few more changes in the algorithm logic, which is why I chose to do it this way.  
Let me know what you prefer. I can implement `has_finite_coordinates`, but this would mean even more code, and I am not sure it is worth it. But it is really up to you.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2015-11-19 20:54:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/335#issuecomment-158194860  

@mkaravel I'm ok with `has_non_finite_coordinate` :)

---

## Comment 6

> Username: mkaravel  
> Created_at: 2015-11-20 13:07:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/335#issuecomment-158396766  

@barendgehrels Are you okay with merging?

---

## Comment 7

> Username: mkaravel  
> Created_at: 2015-11-26 09:33:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/335#issuecomment-159857950  

@barendgehrels I would like to merge this PR so that fix gets into 1.60  
Okay to merge?

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2015-11-26 19:51:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/335#issuecomment-159985744  

I'm OK, thanks for the PR

---
