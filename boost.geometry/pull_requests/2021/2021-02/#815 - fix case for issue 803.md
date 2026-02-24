# #815 [buffer] fix case for issue 803  [Merged]

> Username: barendgehrels  
> Created at: 2021-02-19 13:21:44 UTC  
> Updated at: 2021-02-25 20:26:48 UTC  
> Merged at: 2021-02-25 20:26:42 UTC  
> Closed at: 2021-02-25 20:26:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/815  

Issue #803 had an artifact, generated on nearly collinear lines in the buffer  
  
  
The intersection point (calculated from the green and the blue line) was quite off (magenta) as you can see below.  
The artifact itself is also visible below.  
  
Now I added an option to calculate the intersection point from either those lines, or from the angle bisector between the two perpendicular lines, so from the green line and the center between the two orange lines (here they're nearly collinear)  
  
![ip_wrong](https://user-images.githubusercontent.com/334849/108509749-eddcfb80-72bd-11eb-9c24-5bd8a1d8502d.png)  
  
  
![buffer](https://user-images.githubusercontent.com/334849/108511329-0c43f680-72c0-11eb-9844-0b379691a9d7.png)

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2021-02-19 13:27:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/815#pullrequestreview-594185350  

📁 include/boost/geometry/algorithms/detail/buffer/buffer_inserter.hpp

```diff
 208 |         typename DistanceStrategy,
 205 |-         typename SideStrategy,
 209 |+         typename SegmentStrategy,
```

> Username: barendgehrels  
> Created_at: 2021-02-19 13:27:25 UTC  
> Updated_at: 2021-02-24 11:16:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/815#discussion_r579183716  

This is slightly related (because I had to pass it too), and fix the issue that we had two side-strategies (one real, and one to generate the sides along buffer segments). The last one is now renamed to `SegmentStrategy` in this code.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2021-02-19 13:39:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/815#pullrequestreview-594194950  

📁 include/boost/geometry/algorithms/detail/buffer/line_line_intersection.hpp

```diff
 102 |+             }
 103 |+ 
 104 |+             ip = std::abs(denominator_pq) > std::abs(denominator_pr)
```

> Username: barendgehrels  
> Created_at: 2021-02-19 13:39:04 UTC  
> Updated_at: 2021-02-24 11:16:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/815#discussion_r579191375  

This is the fix

> Username: awulkiew  
> Created_at: 2021-02-23 20:33:27 UTC  
> Updated_at: 2021-02-24 11:16:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/815#discussion_r581368273  

So are we using `std::abs` now instead of `math::abs`?

> Username: barendgehrels  
> Created_at: 2021-02-24 10:54:24 UTC  
> Updated_at: 2021-02-24 11:16:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/815#discussion_r581858859  

Thanks, that was an error. Fixed. :heavy_check_mark:


---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2021-02-19 16:11:02 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/815#pullrequestreview-594330565  

I am ok with merging.

📁 include/boost/geometry/algorithms/detail/buffer/line_line_intersection.hpp

```diff
  93 |+             auto const r = detail::make::make_infinite_line<ct>(vertex, between);
  94 |+ 
  95 |+             auto const denominator_pr = p.b * r.a - p.a * r.b;
```

> Username: vissarion  
> Created_at: 2021-02-19 16:02:33 UTC  
> Updated_at: 2021-02-24 11:16:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/815#discussion_r579294024  

why not using the determinant function?

> Username: barendgehrels  
> Created_at: 2021-02-19 16:19:23 UTC  
> Updated_at: 2021-02-24 11:16:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/815#discussion_r579306210  

> why not using the determinant function?  
  
:+1: I'll change it.  
It was not in the code where I adapted it from - but I can change it there too

> Username: barendgehrels  
> Created_at: 2021-02-24 11:07:00 UTC  
> Updated_at: 2021-02-24 11:16:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/815#discussion_r581867101  

:heavy_check_mark: done  
Calling it directly looked a bit messy, I now added a determinant variant for the infinite line and that looks good IMO.  
Do you agree?

---

📁 include/boost/geometry/algorithms/detail/buffer/line_line_intersection.hpp

```diff
  84 |+         auto const p = detail::make::make_infinite_line<ct>(pi, pj);
  85 |+         auto const q = detail::make::make_infinite_line<ct>(qi, qj);
  86 |+         auto const denominator_pq = p.b * q.a - p.a * q.b;
```

> Username: vissarion  
> Created_at: 2021-02-19 16:03:32 UTC  
> Updated_at: 2021-02-24 11:16:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/815#discussion_r579294676  

why not using the determinant function?

> Username: barendgehrels  
> Created_at: 2021-02-24 11:07:12 UTC  
> Updated_at: 2021-02-24 11:16:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/815#discussion_r581867233  

:heavy_check_mark:


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2021-02-24 11:09:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/815#pullrequestreview-597360687  

📁 include/boost/geometry/arithmetic/infinite_line_functions.hpp

```diff
  41 |+     Point result;
  42 |+     geometry::set<0>(result, determinant<Line, &Line::b, &Line::c>(p, q) / denominator);
  43 |+     geometry::set<1>(result, determinant<Line, &Line::c, &Line::a>(p, q) / denominator);
```

> Username: barendgehrels  
> Created_at: 2021-02-24 11:09:07 UTC  
> Updated_at: 2021-02-24 11:16:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/815#discussion_r581868554  

This shows how determinant (of infinite lines) is called now


---
