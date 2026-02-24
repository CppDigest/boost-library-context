# #769 Fix/start turns [Merged]

> Username: barendgehrels  
> Created at: 2020-10-28 13:45:38 UTC  
> Updated at: 2020-11-04 08:44:14 UTC  
> Merged at: 2020-11-04 08:44:10 UTC  
> Closed at: 2020-11-04 08:44:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/769  

This commit adds start turns (if specified in assign policy, and not for rescaling).  
They are necessary in cases where the corresponding arrival turn is missed by accuracy.  
  
Testcases (added) are found by `recursive_polygons_buffer`  
  
Performance: with start turns:  
```  
$ recursive_polygons_buffer --count 10000 --level 4 --form triangle --validity 0  
Test configuration:  
  - Using Start Turns  
  - Default test type: d  
geometries: 300797 errors: 1501 type: d time: 26.026  
  
Test configuration:  
  - Default test type: d  
geometries: 291375 errors: 2286 type: d time: 24.516  
```  
  
Note, with rescaling there are 0 errors, so I'm still concentrating on buffer. Next round I will look in some of the other added cases in more detail, but they're not related to missing turns but to clusters.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2020-10-28 13:47:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/769#pullrequestreview-518677257  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
 880 |+             int const side_pj_q1 = side.pj_wrt_q1();
 881 |+             ui_else_iu(side_pj_q1 == 1, ti);
 882 |+         }
```

> Username: barendgehrels  
> Created_at: 2020-10-28 13:47:20 UTC  
> Updated_at: 2020-10-28 13:51:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/769#discussion_r513455883  

This code is relatively simple now, I first had more conditions but they were not relevant in the end.  
But I might find cases where some behaviour should be fine-tuned.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2020-10-28 13:48:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/769#pullrequestreview-518678689  

📁 test/algorithms/buffer/buffer_multi_polygon.cpp

```diff
 607 | #if defined(BOOST_GEOMETRY_TEST_FAILURES)
 544 |-     BoostGeometryWriteExpectedFailures(1, 1, 2, 3);
 608 |+     BoostGeometryWriteExpectedFailures(1, 14, 2, 11);
```

> Username: barendgehrels  
> Created_at: 2020-10-28 13:48:44 UTC  
> Updated_at: 2020-10-28 13:51:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/769#discussion_r513456938  

Because it's related to precision, for float these changes were not necessary.  
double/long double add more errors, just because I added the failing cases and part of them are not yet solved.


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2020-11-02 00:35:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/769#pullrequestreview-521280545  

📁 include/boost/geometry/algorithms/detail/overlay/handle_colocations.hpp

```diff
 515 |+         cluster_info& cinfo = nv.second;
 516 |+         auto& indices = cinfo.turn_indices;
 517 |+         std::size_t start_count{0};
```

> Username: awulkiew  
> Created_at: 2020-11-02 00:35:32 UTC  
> Updated_at: 2020-11-02 00:35:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/769#discussion_r515695203  

Out of curiosity, why not `std::size_t start_count = 0;` ?

> Username: barendgehrels  
> Created_at: 2020-11-02 17:00:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/769#discussion_r516117713  

Habit, it the office we're preferring the new uniform initialization.  
  
What do you think?

> Username: mloskot  
> Created_at: 2020-11-02 17:15:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/769#discussion_r516128414  

I used to be very keen in preferring the uniform initialization heavily and everywhere, until I read the abseil guidelines and tips, in particular https://abseil.io/tips/88  
  
> First, “uniform” is a stretch: there are cases where ambiguity still exists (for the casual reader, not the compiler) in what is being called and how.  
>   
> ```  
> std::vector<std::string> strings{2}; // A vector of two empty strings.  
> std::vector<int> ints{2};            // A vector containing only the integer 2.  
> ```  
  
It's a pity the uniform initialization is not corner-case free improvement to C++.

> Username: awulkiew  
> Created_at: 2020-11-02 18:26:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/769#discussion_r516172912  

In this particular case I'd use `= 0` (also for numeric classes) because in such case the variable represents `0`.  
  
This is not a case when an object of a class is created by passing some abstract which may describe something like number of elements.  
  
From the same reason I'd probably initialize the vector like the one below. In other words to express that the initializer list on the right represents the vector or that it is the vector (just like with `0` being the number):  
```  
std::vector<int> ints = {2};  
```  
This works right? ;)  
  
And then for all other things I don't have an opinion. I'd probably use the old parentheses. But maybe that's just because it's what I'm familiar with. Maybe with the exception of avoiding double parentheses to deal with the most vexing parse.  
  
So I guess I could also express this as a conservative stance where old initialization is used when possible as it was in the past and new brackets only for new features.

> Username: barendgehrels  
> Created_at: 2020-11-04 08:43:14 UTC  
> Updated_at: 2020-11-04 08:43:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/769#discussion_r517178058  

Interesting visions, thanks.  
I agree with the statement that it's not that intuitive, `int i{0}` is quite different from all other languages where it is something like `int i=0;`  
I'll think about it. For now I will merge this, assuming these were not objections.


---
