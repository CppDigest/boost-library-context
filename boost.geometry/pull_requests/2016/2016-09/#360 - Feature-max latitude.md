# #360 Feature/max latitude [Closed]

> Username: vissarion  
> Created at: 2016-09-02 14:58:35 UTC  
> Updated at: 2017-03-09 11:03:52 UTC  
> Closed at: 2016-12-02 10:25:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/360  

### Problem: compute the vertex latitude of a segment  
  
Given a segment compute the point with the highest latitude, called vertex latitude. Note that on sphere/ellipsoid this could be higher than the maximum latitude of the segment's endpoints.   
### Solution  
  
This PR implements formulas proposed in   
  
`Wood - Vertex Latitudes on Ellipsoid Geodesics, SIAM Rev., 38(4), 637–644, 1996`   
  
It is implemented in formulas/vertex_latitude.hpp for both geographic and spherical cases. The method computes both maximum and minimum latitude points of the segment. Several tests are also added.  
### Comments  
- The solution is expected to be much faster than computing the shortest distance between the segment and the North (or South) pole. Note that the later is a correct (but slower) solution for the problem.   
- This PR is going to be used by the algorithm that computes the envelope of a segment.   
- Azimuth algorithm for spherical segments was altered in order to use an azimuth formula. This is more consistent to geographic azimuth, i.e. the algorithm calls a formula. Then the azimuth formula is directly be used by the vertex latitude formula.

---

## Comment 1

> Username: vissarion  
> Created_at: 2016-09-12 15:02:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/360#issuecomment-246376740  

I am working in merging this implementation with the one of envelope in BG. I think it is clearer to write a new PR on envelope and delete the current one.

---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2016-12-01 17:17:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/360#pullrequestreview-11012799  

📁 include/boost/geometry/formulas/vertex_latitude.hpp

```diff
 116 |+ 
 117 |+     template <typename T>
 118 |+     inline static void sign_adjastment(CT lat1, CT lat2, CT vertex_lat, T& vrt_result)
```

> Username: barendgehrels  
> Created_at: 2016-12-01 17:17:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/360#discussion_r90493787  

I think these values should be passed as const&. For built-in types this does not make any difference, probably. But for user-defined numeric types it might make a difference. Also below in vertex_on_segment.


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2016-12-01 17:23:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/360#pullrequestreview-11013068  

In general OK, thanks for the PR. See the minor comments

📁 include/boost/geometry/formulas/vertex_latitude.hpp

```diff
  58 |+         CT const alp2 = std::abs(result.reverse_azimuth);
  59 |+ 
  60 |+         if(vertex_on_segment(alp1, alp2, lat1, lat2, vrt_result))
```

> Username: barendgehrels  
> Created_at: 2016-12-01 17:19:06 UTC  
> Updated_at: 2016-12-01 17:23:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/360#discussion_r90494041  

minor: I think we have spaces after if, so if (   
(contrary to function calls where we don't do that)

---

📁 include/boost/geometry/formulas/vertex_latitude.hpp

```diff
  45 |+             typename T2
  46 |+             >
  47 |+     static inline vertex_lat_result spherical(T1 const& lon1,
```

> Username: barendgehrels  
> Created_at: 2016-12-01 17:20:37 UTC  
> Updated_at: 2016-12-01 17:23:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/360#discussion_r90494347  

Also minor, if it fits in one line (80 chars), you may write it on one line (template <typename T1, typename T2>  
If it does not fit on one line, it should be indented like functions do, so < goes on next line, aligned under template

---

📁 include/boost/geometry/formulas/vertex_latitude.hpp

```diff
 107 |+             CT const c = n_b1 * cos_lat1 * sin_alp1;
 108 |+ 
 109 |+             CT const a_c2 = math::sqr(a / c);
```

> Username: barendgehrels  
> Created_at: 2016-12-01 17:21:33 UTC  
> Updated_at: 2016-12-01 17:23:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/360#discussion_r90494508  

who guarantees c is non-zero?

---

📁 include/boost/geometry/formulas/vertex_latitude.hpp

```diff
 108 |+ 
 109 |+             CT const a_c2 = math::sqr(a / c);
 110 |+             CT const vertex_lat = std::asin(math::sqrt((a_c2 - 1) / (a_c2 - e2)));
```

> Username: barendgehrels  
> Created_at: 2016-12-01 17:22:14 UTC  
> Updated_at: 2016-12-01 17:23:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/360#discussion_r90494653  

Same here - is there chance that denumerator gets 0 ?


---

## Comment 4

> Username: vissarion  
> Created_at: 2016-12-02 10:25:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/360#issuecomment-264422327  

This PR is superfluous because of PR "Feature/envelope expand geo #367", thus I am closing it.

---
