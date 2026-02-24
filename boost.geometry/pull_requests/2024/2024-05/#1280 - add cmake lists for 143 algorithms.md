# #1280 [test] add cmake lists for 143 algorithms [Merged]

> Username: barendgehrels  
> Created at: 2024-05-09 09:09:05 UTC  
> Updated at: 2024-05-21 17:45:35 UTC  
> Merged at: 2024-05-21 17:45:31 UTC  
> Closed at: 2024-05-21 17:45:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1280  

This PR  
* adds 143 algorithms  
* because this caused a duplicate in `perimeter` : it moves those two algorithms to their folder  
  
They all pass on my Mac.  
Do we have this CMake flavor on CI already?

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-05-09 09:11:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1280#pullrequestreview-2047516466  

📁 test/algorithms/perimeter/CMakeLists.txt

```diff
   7 |+ foreach(item IN ITEMS
   8 |+     perimeter
   9 |+     perimeter_base
```

> Username: barendgehrels  
> Created_at: 2024-05-09 09:11:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/1280#discussion_r1595189442  

These two above (were duplicate) are quite similar, and quite short, we can merge them later.  
But I wanted this PR to be CMake only.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2024-05-09 09:15:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1280#pullrequestreview-2047523259  

📁 test/algorithms/distance/CMakeLists.txt

```diff
  20 |+     distance_se_geo_pl_pl
  21 |+     distance_se_pl_l
  22 |+     distance_se_pl_pl
```

> Username: barendgehrels  
> Created_at: 2024-05-09 09:15:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1280#discussion_r1595193592  

All these lists are made by using relevant lines from the `Jamfile` and stripping all but the filename.


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2024-05-09 09:16:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1280#pullrequestreview-2047524567  

📁 test/algorithms/closest_points/CMakeLists.txt

```diff
  13 |+     pl_pl
  14 |+     )
  15 |+   boost_geometry_add_unit_test("algorithms_closest_points" ${item})
```

> Username: barendgehrels  
> Created_at: 2024-05-09 09:16:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/1280#discussion_r1595194369  

This one is the only suffixing the name itself to the algorithms, for obvious reasons


---

## Review 4 [Approved]

> Username: vissarion  
> Created_at: 2024-05-10 09:21:59 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1280#pullrequestreview-2049684681  

Thanks, I think it is very useful to have a complete cmake support for tests.   
  
Regarding perimeter tests, should you also move `test_perimeter.hpp` in `perimeter` directory?   
  
There is no cmake related testing in CI, we should include it. See also https://github.com/boostorg/geometry/pull/1273#issuecomment-2102961622

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2024-05-10 15:54:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/1280#issuecomment-2104846290  

> Regarding perimeter tests, should you also move `test_perimeter.hpp` in `perimeter` directory?  
  
Sure, thank you! I missed it. Fixed.

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2024-05-10 15:59:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/1280#issuecomment-2104852800  

> Thanks, I think it is very useful to have a complete cmake support for tests.  
  
The algorithms should be complete now. But there is still some work to do for several folders (among others: `policies` , `iterators`, `formulas`)

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2024-05-10 16:01:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1280#issuecomment-2104855981  

> There is no cmake related testing in CI, we should include it. See also [#1273 (comment)](https://github.com/boostorg/geometry/pull/1273#issuecomment-2102961622)  
  
Thanks, looking forward to it.

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2024-05-11 11:59:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1280#issuecomment-2105700910  

@vissarion   
I automated it a bit (not checked in) with help of ChatGPT, last pushes were from that effort. That also adds `detail/sections` which I missed before.  
  
Next PR will add the next list, and then it should be complete.  
```  
add_subdirectory(core)  
add_subdirectory(concepts)  
add_subdirectory(geometries)  
add_subdirectory(arithmetic)  
add_subdirectory(formulas)  
add_subdirectory(iterators)  
add_subdirectory(strategies)  
add_subdirectory(policies)  
add_subdirectory(io)  
add_subdirectory(srs)  
```

---
