# #711 [buffer] Fix direction code for some new cases in buffer [Merged]

> Username: barendgehrels  
> Created at: 2020-05-06 12:45:05 UTC  
> Updated at: 2020-05-25 21:40:03 UTC  
> Merged at: 2020-05-13 08:10:49 UTC  
> Closed at: 2020-05-13 08:10:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/711  

The similar_direction function could be erroneous for corner cases (related to accuracy),  
refactored it out.  
  
This also makes it a bit less dependent on infinite_line (cartesian).  
  
In the test `recursive_polygons_buffer.cpp` with options `--count 10000 --level 6 --form triangle`   
the error count reduces from ~120 to ~15 (of 10000 cases), so a considerable reduction in wrong buffering. So many these wrong results were apparently caused by this error. Four of these cases, previously going wrong, are added to the unit test.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2020-05-06 12:54:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/711#pullrequestreview-406581528  

📁 include/boost/geometry/arithmetic/infinite_line_functions.hpp

```diff
  90 |- }
  91 |- 
  79 | template <typename Type>
```

> Username: barendgehrels  
> Created_at: 2020-05-06 12:54:03 UTC  
> Updated_at: 2020-05-06 12:54:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/711#discussion_r420766215  

What could go wrong was that (for example) `p.b` was `-1.00e-16` and `q.b` was `+1.00e-16` and therefore their product was negative, while the segments obviously had the same direction...


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2020-05-06 14:25:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/711#pullrequestreview-406665958  

📁 include/boost/geometry/algorithms/detail/buffer/buffer_inserter.hpp

```diff
 187 |-         return arithmetic::similar_direction(p, q);
 176 |+         typedef typename cs_tag<output_point_type>::type cs_tag;
 177 |+         return direction_code<cs_tag>(p0, p1, p2) == 1;
```

> Username: awulkiew  
> Created_at: 2020-05-06 14:25:12 UTC  
> Updated_at: 2020-05-06 14:25:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/711#discussion_r420833521  

In geographic this'd need the earth model right?

> Username: barendgehrels  
> Created_at: 2020-05-06 16:23:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/711#discussion_r420922546  

For this one I'm less sure (I mean: less than expand).  
But I guess it's not necessary here either.  
  
The spherical/geographical implementation is not made by me, and I didn't concentrate on it today, I just saw it was there and calling it makes the functionality less Cartesian dependent (than calling infinite_line).

> Username: awulkiew  
> Created_at: 2020-05-06 16:59:02 UTC  
> Updated_at: 2020-05-06 17:02:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/711#discussion_r420946238  

Ah right, at this point we already know the segments are collinear.


---

## Comment 3

> Username: barendgehrels  
> Created_at: 2020-05-13 08:10:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/711#issuecomment-627823902  

I assume it's OK to merge, it small. If there still is anything I can address it later

---

## Comment 4

> Username: awulkiew  
> Created_at: 2020-05-25 14:26:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/711#issuecomment-633597670  

@barendgehrels This change causes some problems:  
https://circleci.com/api/v1.1/project/github/boostorg/geometry/8170/output/102/0?file=true

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2020-05-25 21:40:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/711#issuecomment-633723449  

>   
>   
> @barendgehrels This change causes some problems:  
> https://circleci.com/api/v1.1/project/github/boostorg/geometry/8170/output/102/0?file=true  
  
Thanks I'll have a look on Wednesday

---
