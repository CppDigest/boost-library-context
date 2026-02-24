# #1286 [test] complete cmake coverage [Merged]

> Username: barendgehrels  
> Created at: 2024-05-22 08:20:42 UTC  
> Updated at: 2024-12-14 11:13:28 UTC  
> Merged at: 2024-07-24 14:53:44 UTC  
> Closed at: 2024-07-24 14:53:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/1286  

This makes CMake coverage complete for unit tests.  
The CMakeLists are generated automatically, and manually edited where indicated.  
There is one test added to the Jam suite as well, as indicated.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-05-22 08:21:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1286#pullrequestreview-2070490715  

📁 test/formulas/CMakeLists.txt

```diff
  15 |+ endforeach()
  16 |+ 
  17 |+ if (NOT APPLE)
```

> Username: barendgehrels  
> Created_at: 2024-05-22 08:21:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1286#discussion_r1609519266  

Manual edit


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2024-05-22 08:21:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1286#pullrequestreview-2070492898  

📁 test/geometries/CMakeLists.txt

```diff
  16 |+     box
  17 |+     custom_linestring
  18 |+     # initialization
```

> Username: barendgehrels  
> Created_at: 2024-05-22 08:21:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1286#discussion_r1609520851  

The two commented tests would need extra dependencies (for test only).


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2024-05-22 08:22:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1286#pullrequestreview-2070494282  

📁 test/io/Jamfile

```diff
  15 | 
  16 |- build-project wkt ; 
  16 |+ build-project dsv ;
```

> Username: barendgehrels  
> Created_at: 2024-05-22 08:22:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1286#discussion_r1609521752  

The `dsv` Jamfile was there, but not run. For CMake it runs without problems - so I added it for Jam too


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2024-05-22 08:22:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1286#pullrequestreview-2070494975  

📁 test/iterators/CMakeLists.txt

```diff
   9 |+     # concatenate_iterator
  10 |+     ever_circling_iterator
  11 |+     # flatten_iterator
```

> Username: barendgehrels  
> Created_at: 2024-05-22 08:22:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1286#discussion_r1609522193  

Two manual edits, needing extra dependencies


---

## Review 5 [Commented]

> Username: barendgehrels  
> Created_at: 2024-05-22 08:23:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1286#pullrequestreview-2070495757  

📁 test/views/CMakeLists.txt

```diff
  14 |+     segment_view
  15 |+   )
  16 |+   boost_geometry_add_unit_test("views" ${item})
```

> Username: barendgehrels  
> Created_at: 2024-05-22 08:23:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1286#discussion_r1609522650  

Format change only


---

## Review 6 [Approved]

> Username: vissarion  
> Created_at: 2024-05-22 09:49:34 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1286#pullrequestreview-2070696444  

Thanks, it is great to have full cmake support.  
  
There is also a related cmake issue https://github.com/boostorg/geometry/issues/1282 but that could be handled in different PR.

---
