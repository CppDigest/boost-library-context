# #66 [voronoi] pps() is treating site3 as an infinite line [Closed]

> Username: eadf  
> Created at: 2021-07-10 15:56:36 UTC  
> Updated at: 2023-10-28 16:48:24 UTC  
> Closed at: 2023-10-28 16:48:24 UTC  
> Url: https://github.com/boostorg/polygon/pull/66  

This voronoi example:  
```  
0  
3  
-5138 -5149 -5038 -5142  
-5042 -5069 -5165 -5162  
-5011 -5195 -5404 -5134  
```  
Will get this result from 1.76.0:  
![1](https://user-images.githubusercontent.com/655495/125167305-25cad980-e1a0-11eb-9593-b37f836719a2.png)  
  
The problem is that `pps()` is treating segments as a lines of infinite length.  
  
Here is an illustration of the problem:  
`site1` and `site2` are the black and blue dots.  
`site3` is the green line. `site3.point0` is the dark green dot.  
![solution](https://user-images.githubusercontent.com/655495/125167345-50b52d80-e1a0-11eb-9d6a-ade935b8c62f.png)  
`pps()` will find the red circle event, and that would have been correct if `site3` were just a little bit longer.  
`pps()` assumes that `site3` tangents the CE it is looking for, but that's not always the case.  
The correct circle event, the one passing through `site1`, `site2` *and* `site3` is the cyan colored circle.  
  
  
My proposed solution to this problem is to take the CE `pps()` finds and project the (`site3.point0` ->  CE) vector upon the (`site3.point0`->`site3.point1`) vector.   
If the length of the projection is larger (or negative) than the `site3` vector, the real CE is re-calculated using `ppp()`.  
  
The 'cost' of this fix is an additional calculation of a dot product for every call to `pps()` + the rare occasion when `ppp()` actually needs to be called.  
  
To be able to call `ppp()` with either `site3.point0 `or `site3.point1` I had to change the method signature of `ppp()` to accept points instead of sites.  
  
Here are more examples of the problem. (this PR fixes 'em all)  
```  
0  
3  
-5205 -5210 -5095 -5152  
-5166 -5197 -5099 -5209  
-5029 -5002 -5500 -5319  
```  
  
![2](https://user-images.githubusercontent.com/655495/125167840-d0dc9280-e1a2-11eb-8fa9-e9d0c21376bd.png)  
  
```  
0  
3  
759 -242 631 128  
189 -303 843 693  
-911 -920 921 853  
```  
![3](https://user-images.githubusercontent.com/655495/125167850-d9cd6400-e1a2-11eb-872b-bd968caaadd5.png)  
  
  
```  
0  
3  
580 -833 552 -566  
-671 955 604 -936  
535 -110 412 -549  
```  
  
![4](https://user-images.githubusercontent.com/655495/125167857-df2aae80-e1a2-11eb-8905-abcbba7cfc70.png)  
  
  
```  
0  
3  
963 -74 -944 707  
694 281 853 211  
326 220 803 441  
```  
  
![5](https://user-images.githubusercontent.com/655495/125167860-e487f900-e1a2-11eb-922a-ba99f382596b.png)  
  
```  
0  
3  
415 -54 955 703  
976 38 -916 -467  
909 424 962 401  
```  
![6](https://user-images.githubusercontent.com/655495/125167865-e94cad00-e1a2-11eb-953e-cccf123a5d1b.png)  
  
```  
0  
3  
365 113 741 366  
768 -67 601 187  
-814 662 817 -285  
```  
![7](https://user-images.githubusercontent.com/655495/125167871-ee116100-e1a2-11eb-9b44-00b5a386bd81.png)

---

## Comment 1

> Username: asydorchuk  
> Created_at: 2021-07-13 09:15:45 UTC  
> Updated_at: 2021-07-13 09:16:04 UTC  
> Url: https://github.com/boostorg/polygon/pull/66#issuecomment-878920787  

Hi, I will start looking into this and probably will get back with some questions. Thanks for the PR!

---

## Comment 2

> Username: eadf  
> Created_at: 2021-07-13 09:29:41 UTC  
> Updated_at: 2021-07-13 14:22:39 UTC  
> Url: https://github.com/boostorg/polygon/pull/66#issuecomment-878930827  

The same problem exists for `pss()` but it's much more involved, lots of edge cases that I have not figured out yet. #67

---

## Comment 3

> Username: eadf  
> Created_at: 2021-12-03 15:32:01 UTC  
> Updated_at: 2021-12-04 17:46:23 UTC  
> Url: https://github.com/boostorg/polygon/pull/66#issuecomment-985614198  

I've changed the PR so that `pps()` does not delegate to `ppp()` anymore. That solution had a potential of reporting false circle events because `ppp()` simply does not have the required information to prevent a CE from intersecting the segment. (only tangent points are allowed, or intersection at end points)  
With this fix, the CE is reported as "not found"  if it does not reach any valid point of the segment.   
(Just like `circle_existence_predicate_.pps()` does)  
  
In addition to the test-cases listed above, this solution also fixes:  
```  
0  
3  
519 -635 342 -158  
-440 707 661 -976  
269 -113 507 -171  
```   
(this is one of the test cases I thought should be fixed by a future `pss()` fix #67)  
  
By comparing identical circle events by their age, the `pss()` cases and more cases *could* be fixed as well:  
```  
0  
3  
673 903 -985 -362  
-238 248 -179 453  
-136 323 -90 419  
```  
  
```  
0  
3  
597 -23 533 65  
716 302 455 245  
-716 412 999 356  
```  
  
```  
0  
3  
570 8 245 194  
838 785 8 157  
-965 -572 934 858  
```  
  
```  
0  
3  
310 407 365 177  
754 177 -893 79  
300 558 109 347  
```  
  
```  
0  
3  
503 -633 600 -981  
664 -702 688 -800  
-658 584 880 -850  
```  
  
```  
0  
3  
180 217 436 -48  
561 725 -777 -922  
-120 -155 443 558  
```  
  
```  
0  
3  
-649 -607 956 199  
153 3 13 -252  
89 -186 293 -40  
```  
  
But it turns out that cases like this, that works w/o the identical-CE-age-ordering, will break:  
```  
0  
3  
523 224 465 223  
-852 -312 853 292  
277 267 369 449  
```  
  
```  
0  
3  
-467 -299 603 -254  
970 -188 -774 -316  
238 -271 377 -590  
```  
  
```  
0  
3  
836 737 -603 -397  
207 253 585 581  
61 177 286 550  
```  
  
At least we got a lead on what's causing the problems: the ordering of coordinate-identical circle events.

---

## Review 4 [Commented]

> Username: Unknown  
> Created_at: 2021-12-08 19:19:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/polygon/pull/66#pullrequestreview-826863844  

📁 include/boost/polygon/detail/voronoi_predicates.hpp

```diff
1131 |       }
1132 |+       // All points needs to be unique or the dot product calculation will not be valid
1133 |+       bool unique_endpoints = !(site1.point0() == site2.point0() ||
```

> Username: Unknown  
> Created_at: 2021-12-08 19:19:31 UTC  
> Url: https://github.com/boostorg/polygon/pull/66#discussion_r765176741  

Do we actually actually need this now that you are not calling `ppp` anymore?   
i.e. don't we only want that `site3.is_segment() == true`? (which should be required for calling `pps` anyway)

> Username: eadf  
> Created_at: 2021-12-08 19:46:14 UTC  
> Updated_at: 2021-12-10 00:57:43 UTC  
> Url: https://github.com/boostorg/polygon/pull/66#discussion_r765194825  

without a unique endpoint test the diagram generation will fail on examples like   
```  
0  
3  
-5138 -5149 -5038 -5142  
-5042 -5069 -5165 -5162  
-5011 -5195 -5404 -5134  
```  
or   
```  
0  
2  
-5093 -5402 -5362 -5008  
-5200 -5273 -5273 -5265  
```  
(I got more examples if you need them) (It's interesting that none of those examples uses shared end-points, and yet they fail)  
  
I'm not exactly sure why, but I guess it ties in to your follow up question:  
  
> That can lead to missing voronoi vertices right?  
  
Yes


---

## Review 5 [Commented]

> Username: Unknown  
> Created_at: 2021-12-08 19:34:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/polygon/pull/66#pullrequestreview-826877464  

📁 include/boost/polygon/detail/voronoi_predicates.hpp

```diff
1046 | 
1047 |-     void pps(const site_type& site1,
1047 |+     bool pps(const site_type& site1,
```

> Username: Unknown  
> Created_at: 2021-12-08 19:34:35 UTC  
> Url: https://github.com/boostorg/polygon/pull/66#discussion_r765187126  

Just a suggestion: instead of having `pps()` (and the other ones) return a bool, could we perhaps check the circle downstream, say where `lies_outside_vertical_segment()` is called?  
Something like:   
```  
            if (!site_touches_circle(circle, site1) || !site_touches_circle(circle, site2) ||  
                !site_touches_circle(circle, site3)) {  
                return false;  
            }  
```  
with:  
```  
        bool site_touches_circle(const circle_type &c, const site_type &s) {  
            fpt_type v0c[2] = {c.x() - to_fpt(s.point0().x()), c.y() - to_fpt(s.point0().y())};  
            if (!s.is_segment()) {  
                fpt_type square_dist = v0c[0] * v0c[0] + v0c[1] * v0c[1];  
                return ulp_cmp(square_dist, (c.lower_x() - c.x()) * (c.lower_x() - c.x()), ULPS) == ulp_cmp_type::EQUAL;  
            }  
            fpt_type v01[2] = {to_fpt(s.point1().x()) - to_fpt(s.point0().x()),  
                               to_fpt(s.point1().y()) - to_fpt(s.point0().y())};  
            fpt_type dot = (v0c[0] * v01[0] + v0c[1] * v01[1]) / (v01[0] * v01[0] + v01[1] * v01[1]);  
            return ulp_cmp(dot, 0.0, ULPS) != ulp_cmp_type::LESS && ulp_cmp(dot, 1.0, ULPS) != ulp_cmp_type::MORE;  
        }  
```  
  
However, it is probably desirable that if `circle_existence_predicate_.pps()` returns `true` then `circle_formation_functor_.pps()` has to compute a valid circle event (i.e. not a NaN and all three sites touching the circle) and we should then assert it instead of returning `false`.

> Username: eadf  
> Created_at: 2021-12-08 19:56:54 UTC  
> Url: https://github.com/boostorg/polygon/pull/66#discussion_r765201815  

Sure, that is an option. I just added the test in `pps()` itself so I did not have to repeat the same code 3 times (in code text).   
  
> and we should then assert it instead of returning `false`.  
  
Yes, this PR does not solve the root issue. It just fixes a few edge cases.


---

## Comment 6

> Username: ghost  
> Created_at: 2021-12-08 19:36:31 UTC  
> Url: https://github.com/boostorg/polygon/pull/66#issuecomment-989135135  

Thanks @eadf !  
What happens if we return `false` for a circle event that we should have been able to calculate? That can lead to missing voronoi vertices right?

---

## Comment 7

> Username: eadf  
> Created_at: 2021-12-08 20:11:23 UTC  
> Url: https://github.com/boostorg/polygon/pull/66#issuecomment-989159470  

> What happens if we return false for a circle event that we should have been able to calculate? That can lead to missing voronoi vertices right?  
  
Yes. I tried the same fix for `pss()` and it frequently fails on previously good solutions. Probably because of that issue.   
  
The best thing would be if `sss()`, `pss()` and `pps()` always returned valid solutions (or nothing)

---
