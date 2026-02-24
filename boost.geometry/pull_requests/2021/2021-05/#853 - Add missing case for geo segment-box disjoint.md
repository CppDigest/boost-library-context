# #853 Add missing case for geo segment-box disjoint [Merged]

> Username: vissarion  
> Created at: 2021-05-24 13:36:04 UTC  
> Updated at: 2021-05-26 07:49:12 UTC  
> Merged at: 2021-05-26 07:49:06 UTC  
> Closed at: 2021-05-26 07:49:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/853  

This PR proposes a fix for https://github.com/boostorg/geometry/issues/851

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2021-05-24 17:34:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/853#pullrequestreview-667001226  

📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
 189 |-         if (!(b0 && b1 && b2 && b3) && (b0 || b1 || b2 || b3))
 189 |+         bool all_ones = b0 && b1 && b2 && b3;
 190 |+         bool all_zeros = !(b0 || b1 || b2 || b3);
```

> Username: awulkiew  
> Created_at: 2021-05-24 17:34:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/853#discussion_r638143608  

`azimuth_side_value` can return values in {-1, 0, 1} meaning {right, on_segment, left} respectively so this condition doesn't mean `all_zeros` It means `not_all_ones` or `either_minus_ones_or_zeros`.

> Username: vissarion  
> Created_at: 2021-05-25 08:33:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/853#discussion_r638574234  

By "zeros" and "ones" I had in mind the values of b1, ..., b3. I see how this is misleading. I rephrased it.


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2021-05-24 17:40:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/853#pullrequestreview-667006193  

📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
 197 |+ 
 198 |+         if ((!all_ones && (b0 || b1 || b2 || b3) && vertex_north) ||
 199 |+             (!all_zeros && !(b0 && b1 && b2 && b3) && !vertex_north))
```

> Username: awulkiew  
> Created_at: 2021-05-24 17:40:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/853#discussion_r638147480  

For now leaving the fact that `all_zeros` can be `true` if side values are `-1` this expression could be simplified to:  
```  
(!all_ones && !all_zeros && vertex_north) || (!all_zeros && all_ones && !vertex_north)  
```

> Username: vissarion  
> Created_at: 2021-05-25 08:36:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/853#discussion_r638576218  

nice catch! actually   
```  
(!all_ones && !all_zeros && vertex_north) || (!all_zeros && !all_ones && !vertex_north)  
```  
(there was a typo above)   
Which can be further simplified to `(!all_ones && !all_zeros)`  
  
I also added the special case of some side value is equal to `0`.


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2021-05-25 09:39:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/853#pullrequestreview-667610784  

📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
 185 |+         auto b1 = formula::azimuth_side_value(alp1, a_b1);
 186 |+         auto b2 = formula::azimuth_side_value(alp1, a_b2);
 187 |+         auto b3 = formula::azimuth_side_value(alp1, a_b3);
```

> Username: awulkiew  
> Created_at: 2021-05-25 09:39:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/853#discussion_r638625732  

The result type is known native type - `int`. It doesn't required to instantiate any template. It's even shorter than `auto`. I think that in such cases `auto` shouldn't be used.

> Username: awulkiew  
> Created_at: 2021-05-25 09:41:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/853#discussion_r638627167  

Btw, since you moved from `bool` to `int` these variables are now side value so maybe it'd be good to rename them to `s0`, `s1`, etc.?


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2021-05-25 09:43:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/853#pullrequestreview-667615179  

📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
 199 |+         bool all_positive = b0_positive && b1_positive && b2_positive && b3_positive;
 200 |+         bool all_non_positive = !(b0_positive || b1_positive || b2_positive || b3_positive);
 201 |+         bool vertex_north = lat1 + lat2 > 0;
```

> Username: awulkiew  
> Created_at: 2021-05-25 09:43:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/853#discussion_r638629005  

This will return `false` strictly (not using epsilon) for a segment at equator right? Can this be a problem?

> Username: vissarion  
> Created_at: 2021-05-25 10:20:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/853#discussion_r638656297  

I think not, the only intersection case will land correctly in the intersection case. I added relevant test cases.


---
