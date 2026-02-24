# #1302 Update cmake scripts and add them to github actions [Merged]

> Username: vissarion  
> Created at: 2024-08-14 12:04:51 UTC  
> Updated at: 2025-05-07 23:25:56 UTC  
> Merged at: 2024-09-13 13:03:15 UTC  
> Closed at: 2024-09-13 13:03:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1302  

This PR:   
  
- creates github actions for cmake builds and tests (as discussed in https://github.com/boostorg/geometry/pull/1280#issuecomment-2104855981)   
- declares tests EXCLUDE_FROM_ALL (closes https://github.com/boostorg/geometry/issues/1282)  
- tests compilation with C++17 (with cmake is much simpler to separate building from running tests, see https://github.com/boostorg/geometry/pull/1273)  
- adds some missing tests in cmake (such as index)

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2024-08-20 06:29:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/1302#issuecomment-2298066706  

Cool PR, I review later

---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2024-08-24 07:20:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1302#pullrequestreview-2258442051  

📁 test/CMakeLists.txt

```diff
  37 |-   add_test(NAME ${unit_test_name} COMMAND ${unit_test_name})
  45 |+   if (NOT ("boost_geometry_${prefix}_${item}" STREQUAL "boost_geometry_cs_undefined_setops1") AND
  46 |+       NOT ("boost_geometry_${prefix}_${item}" STREQUAL "boost_geometry_cs_undefined_setops2"))
```

> Username: barendgehrels  
> Created_at: 2024-08-24 07:20:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1302#discussion_r1729788393  

Isn't this too specific?

> Username: barendgehrels  
> Created_at: 2024-08-24 07:43:10 UTC  
> Updated_at: 2024-08-24 07:43:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/1302#discussion_r1729792595  

And why is it excluded. I get a memory violation - so that might be the reason.  
Can't we pass an optional boolean variable to this function, to add to the test suite, or not?  
(and then for ones like this we should not, but with a comment why not).  
  
And then, probably, it can still be added as a unit test - but not as dependency to the custom `tests` target

> Username: vissarion  
> Created_at: 2024-09-05 13:54:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1302#discussion_r1745589685  

Done, thanks.


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2024-08-24 07:44:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1302#pullrequestreview-2258454263  

📁 test/cs_undefined/CMakeLists.txt

```diff
  19 |+   )
  20 |+   boost_geometry_add_unit_test("cs_undefined" ${item})
  21 |+ endforeach()
```

> Username: barendgehrels  
> Created_at: 2024-08-24 07:44:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/1302#discussion_r1729792672  

So, in line with my comment above, you would get a second loop here  
  
```  
foreach(item IN ITEMS  
    setops1  
    setops2  
  )  
  boost_geometry_add_unit_test("cs_undefined" ${item} FALSE)  
endforeach()  
```

> Username: vissarion  
> Created_at: 2024-09-05 13:53:24 UTC  
> Updated_at: 2024-09-05 13:53:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1302#discussion_r1745588302  

Thanks, I added then one by one though.


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2024-08-24 07:46:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1302#pullrequestreview-2258454728  

📁 test/robustness/overlay/areal_areal/CMakeLists.txt

```diff
  22 |+       "${PROJECT_SOURCE_DIR}/test/robustness"
  23 |+       .)
  24 |+ endforeach()
```

> Username: barendgehrels  
> Created_at: 2024-08-24 07:46:58 UTC  
> Updated_at: 2024-08-24 07:46:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1302#discussion_r1729793064  

These are not unit tests. These are programs.  
I think we need to add a specific function for them.  
  
But anyway, great they are at least compiled now.


---

## Review 5 [Approved]

> Username: barendgehrels  
> Created_at: 2024-08-24 07:47:48 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1302#pullrequestreview-2258454824  

Thanks!  
  
I made some comments, which can be applied either now, or in a subsequent PR (doesn't have to be you, I can also take care maybe) is also fine.

---

## Review 6 [Commented]

> Username: barendgehrels  
> Created_at: 2024-08-24 07:49:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1302#pullrequestreview-2258455030  

📁 test/CMakeLists.txt

```diff
  40 | 
  41 |   # To compile with C++14
  42 |   target_compile_features(${unit_test_name} PRIVATE cxx_std_14)
```

> Username: barendgehrels  
> Created_at: 2024-08-24 07:49:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1302#discussion_r1729793431  

The PR description mentions: `test compilation with C++17`   
But won't this setting override that?

> Username: vissarion  
> Created_at: 2024-09-06 10:10:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1302#discussion_r1746882281  

It seems that cmake selects the highest version of the standard specified in both CMAKE_CXX_STANDARD and target_compile_features.  
see also https://discourse.cmake.org/t/how-to-handle-different-cxx-standards-in-the-same-project/6510/6  
  
However, it is not very neat to mix CMAKE_CXX_STANDARD and target_compile_features. As the code is right now I think it is simpler to just use CMAKE_CXX_STANDARD and avoid target_compile_features, as the former only needed to be set once while the latter need to be set for each individual test. But maybe for this PR it is OK as is. What do you think?

> Username: barendgehrels  
> Created_at: 2024-09-13 12:38:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/1302#discussion_r1758793329  

I agree, maybe we should just use `CMAKE_CXX_STANDARD`  
And for this PR, it is OK as it is.  
  
Agreed.


---

## Review 7 [Commented]

> Username: barendgehrels  
> Created_at: 2024-08-24 07:53:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1302#pullrequestreview-2258455467  

📁 test/robustness/overlay/areal_areal/CMakeLists.txt

```diff
  18 |+   )
  19 |+   boost_geometry_add_unit_test("robustness" ${item})
  20 |+   target_include_directories(boost_geometry_robustness_${item}
```

> Username: barendgehrels  
> Created_at: 2024-08-24 07:53:45 UTC  
> Updated_at: 2024-08-24 07:54:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1302#discussion_r1729793853  

You might use `${BOOST_GEOMETRY_UNIT_TEST_NAME}`, it is set by previous function for this purpose


---

## Comment 8

> Username: vissarion  
> Created_at: 2024-09-06 10:11:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1302#issuecomment-2333730935  

@barendgehrels thansk for the review and the comments. I addressed them all. Only the discussion on the c++ standard needs some check.

---

## Review 9 [Commented]

> Username: barendgehrels  
> Created_at: 2024-09-13 12:40:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1302#pullrequestreview-2303021366  

📁 test/CMakeLists.txt

```diff
  37 |-   add_test(NAME ${unit_test_name} COMMAND ${unit_test_name})
  45 |+   set(extra_macro_args ${ARGN})
  46 |+   if (NOT extra_macro_args STREQUAL "not_run")
```

> Username: barendgehrels  
> Created_at: 2024-09-13 12:40:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/1302#discussion_r1758795067  

👍


---

## Comment 10

> Username: barendgehrels  
> Created_at: 2024-09-13 12:41:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1302#issuecomment-2348864976  

Thanks for handling everything, completely fine!

---

## Comment 11

> Username: Osyotr  
> Created_at: 2025-05-07 23:23:51 UTC  
> Updated_at: 2025-05-07 23:25:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/1302#issuecomment-2860682755  

Why does `boost::geometry` depend on `Boost::program_options` and `Boost::crc` now? It shouldn't be in the public interface as it's only used in tests.

---
