# #1407 test: exclude failing aimes test cases [Merged]

> Username: barendgehrels  
> Created at: 2025-05-10 09:28:33 UTC  
> Updated at: 2025-07-16 15:59:43 UTC  
> Merged at: 2025-05-13 16:34:05 UTC  
> Closed at: 2025-05-13 16:34:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/1407  

addresses: #1403  
  
The Aimes test cases are a set of complex real life features which are not yet totally robust for geographic buffers.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-10 09:28:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1407#pullrequestreview-2830663900  

📁 test/algorithms/buffer/CMakeLists.txt

```diff
  30 |     buffer_point_geo
  31 |     buffer_polygon_geo
  32 |+     buffer_linestring
```

> Username: barendgehrels  
> Created_at: 2025-05-10 09:28:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1407#discussion_r2083080327  

This one was fine without adaptions


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-10 09:30:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1407#pullrequestreview-2830664750  

📁 test/algorithms/buffer/CMakeLists.txt

```diff
  31 |     buffer_polygon_geo
  32 |+     buffer_linestring
  33 |+     buffer_linestring_geo
```

> Username: barendgehrels  
> Created_at: 2025-05-10 09:30:35 UTC  
> Updated_at: 2025-05-10 09:30:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/1407#discussion_r2083081110  

For this case I had to exclude 5 cases.  
  
I will investigate them later this year, after integrating some other enhancements (robust side, #1404, ...)


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-10 09:31:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1407#pullrequestreview-2830664831  

📁 test/algorithms/buffer/buffer_linestring_geo.cpp

```diff
 174 |     test_linestring<bg::strategy::thomas, true, bg::model::point<default_test_type, 2, bg::cs::geographic<bg::degree> > >();
 175 | 
 176 |+     test_linestring_aimes<bg::strategy::andoyer, true, bg::model::point<default_test_type, 2, bg::cs::geographic<bg::degree> > >();
```

> Username: barendgehrels  
> Created_at: 2025-05-10 09:31:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/1407#discussion_r2083081205  

This is not run for `thomas` for speed and complexity reasons


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-10 09:32:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1407#pullrequestreview-2830665036  

📁 test/algorithms/buffer/buffer_linestring_geo.cpp

```diff
 119 |+     std::set<int> const skip_cases_round_round{17, 22, 38, 181, 196};
 120 |+     std::set<int> const skip_cases_round_flat{17, 22, 38, 103, 196};
 121 |+     std::set<int> const skip_cases_miter_flat{17, 18, 22, 38, 103, 196};
```

> Username: barendgehrels  
> Created_at: 2025-05-10 09:32:28 UTC  
> Updated_at: 2025-05-10 09:32:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1407#discussion_r2083081414  

196 was already skipped (in another way, see below).  
Two other cases were already skipped but are now fine.


---

## Review 5 [Approved]

> Username: vissarion  
> Created_at: 2025-05-12 08:56:39 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1407#pullrequestreview-2832414741  

Thanks, I am ok with merging, but should we leave the issue open for tracking?

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2025-05-13 16:33:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1407#issuecomment-2877231710  

> Thanks, I am ok with merging, but should we leave the issue open for tracking?  
  
That's fine I'll adapt the wording.

---
