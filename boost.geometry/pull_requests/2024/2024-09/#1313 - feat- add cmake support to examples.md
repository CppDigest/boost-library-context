# #1313 feat: add cmake support to examples [Merged]

> Username: barendgehrels  
> Created at: 2024-09-26 20:13:58 UTC  
> Updated at: 2024-12-14 11:13:08 UTC  
> Merged at: 2024-09-30 18:14:25 UTC  
> Closed at: 2024-09-30 18:14:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1313  

- replace BOOST_FOREACH  
  
- replace Boost.Assign  
  
- replace typedef with using  
  
- other minor changes

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-09-26 20:15:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1313#pullrequestreview-2332265965  

📁 doc/src/examples/algorithms/clear.cpp

```diff
  51 | [pre
  59 |- (((0, 0), (0, 10), (11, 11), (0, 0)), ((0, 0), (0, 10), (11, 11), (0, 0)))
  52 |+ (((0, 0), (0, 10), (10, 10), (10, 0), (0, 0)), ((1, 2), (8, 2), (4, 6), (1, 2)))
```

> Username: barendgehrels  
> Created_at: 2024-09-26 20:15:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1313#discussion_r1777683006  

This example was incorrect, the output did not correspond with the code


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2024-09-26 20:16:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1313#pullrequestreview-2332268540  

📁 doc/src/examples/algorithms/for_each_point.cpp

```diff
  36 |+                 set<0>(p, round(get<0>(p)));
  37 |+                 set<1>(p, round(get<1>(p)));
  38 |+             }
```

> Username: barendgehrels  
> Created_at: 2024-09-26 20:16:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1313#discussion_r1777684586  

Replaced with lambda


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2024-09-26 20:17:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1313#pullrequestreview-2332268855  

📁 doc/src/examples/algorithms/for_each_segment_const.cpp

```diff
  36 |+             const auto length = boost::geometry::length(s);
  37 |+             min_length = std::min(min_length, length);
  38 |+             max_length = std::max(max_length, length);
```

> Username: barendgehrels  
> Created_at: 2024-09-26 20:17:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1313#discussion_r1777684775  

Replaced with lambda


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2024-09-26 20:18:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1313#pullrequestreview-2332270495  

📁 doc/src/examples/algorithms/make_2d_point.cpp

```diff
  41 |+     construct_and_display<boost::geometry::model::d2::point_xy<double>>();
  42 |+     construct_and_display<boost::geometry::model::d2::point_xy<int>>();
  43 |+     construct_and_display<boost::tuple<double, double>>();
```

> Username: barendgehrels  
> Created_at: 2024-09-26 20:18:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/1313#discussion_r1777685780  

`Boost.Polygon` is removed. The dependency is removed. This is probably not a use case for anyone, anymore.


---

## Review 5 [Commented]

> Username: barendgehrels  
> Created_at: 2024-09-26 20:22:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1313#pullrequestreview-2332276645  

📁 doc/src/examples/io/svg.cpp

```diff
  12 |+ //` Make sure to first call only the method [^add] and then call only the methods [^map] or [^text]. 
  13 |+ //` Because [^add] calculates the bounding box internally and that should be finished
  14 |+ //` before calling mapping functions. This might require two loops.
```

> Username: barendgehrels  
> Created_at: 2024-09-26 20:22:02 UTC  
> Updated_at: 2024-09-26 20:22:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1313#discussion_r1777689610  

Oops, this is also part of the other PR (I continued with that one). Anyway, no problem, will be harmonized

> Username: barendgehrels  
> Created_at: 2024-09-28 08:10:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1313#discussion_r1779441372  

✅


---

## Review 6 [Commented]

> Username: barendgehrels  
> Created_at: 2024-09-26 20:28:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1313#pullrequestreview-2332288326  

📁 doc/src/examples/geometries/CMakeLists.txt

```diff
  22 |+   
  23 |+ endforeach()
  24 |+ 
```

> Username: barendgehrels  
> Created_at: 2024-09-26 20:28:29 UTC  
> Updated_at: 2024-09-26 20:28:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/1313#discussion_r1777697115  

Still needs to add adapted examples - will be added in a separate PR

> Username: barendgehrels  
> Created_at: 2024-09-28 08:03:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/1313#discussion_r1779440373  

✅ added in this PR


---

## Review 7 [Commented]

> Username: vissarion  
> Created_at: 2024-09-27 09:34:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1313#pullrequestreview-2333246683  

Great addition, and modernization! Thanks!  
  
I have only a question. Also it seems that something is missing and some examples cannot be compiled in CI.

📁 doc/src/examples/algorithms/CMakeLists.txt

```diff
  40 |+     intersection_segment
  41 |+     intersects_linestring
  42 |+     is_simple
```

> Username: vissarion  
> Created_at: 2024-09-27 09:33:07 UTC  
> Updated_at: 2024-09-27 09:34:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1313#discussion_r1778326393  

Is this the complete list? For example, it seems that `is_empty` is missing. I am counting 82 items in the examples directory but here there are only 60 in `foreach` loop.

> Username: barendgehrels  
> Created_at: 2024-09-27 15:56:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/1313#discussion_r1778844038  

Oh, I took over what was in the Jamfile.  
So there are 22 more. I will add them.

> Username: barendgehrels  
> Created_at: 2024-09-28 07:50:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1313#discussion_r1779438582  

✅ added most of them. Some of them are not even used (added that as a comment).  
An ancient one which was not used is deleted.


---

## Comment 8

> Username: barendgehrels  
> Created_at: 2024-09-27 16:07:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1313#issuecomment-2379621698  

> Great addition, and modernization! Thanks!  
>   
> I have only a question. Also it seems that something is missing and some examples cannot be compiled in CI.  
  
Indeed, will handle. Thanks.

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2024-09-28 07:50:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1313#issuecomment-2380555510  

> > I have only a question. Also it seems that something is missing and some examples cannot be compiled in CI.  
>   
> Indeed, will handle. Thanks.  
  
✅

---

## Review 10 [Commented]

> Username: barendgehrels  
> Created_at: 2024-09-28 08:07:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1313#pullrequestreview-2334966381  

> Username: barendgehrels  
> Created_at: 2024-09-28 08:07:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1313#discussion_r1779440823  

It didn't compile, is not used, and was so old that it was not worth to fix it here


---

## Review 11 [Commented]

> Username: barendgehrels  
> Created_at: 2024-09-28 08:09:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1313#pullrequestreview-2334966615  

📁 doc/src/examples/algorithms/simplify_insert.cpp

```diff
  32 |-     boost::geometry::simplify_inserter(line, std::back_inserter(simplified), 0.5);
  25 |+     
  26 |+     boost::geometry::detail::simplify::simplify_insert(line, std::back_inserter(simplified), 0.5);
```

> Username: barendgehrels  
> Created_at: 2024-09-28 08:09:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1313#discussion_r1779441116  

We probably should just delete all examples using `detail`, OR promote it to the general API (such as `assign_box_corners`). But not this one probably.


---

## Review 12 [Approved]

> Username: vissarion  
> Created_at: 2024-09-30 09:03:13 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1313#pullrequestreview-2336829986  

Thanks, now all examples (except from problematic ones where comments are inserted) are included and the test are passing.

---
