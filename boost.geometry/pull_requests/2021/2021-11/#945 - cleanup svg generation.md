# #945 [test] cleanup svg generation [Merged]

> Username: barendgehrels  
> Created at: 2021-11-24 12:55:16 UTC  
> Updated at: 2021-12-01 09:09:55 UTC  
> Merged at: 2021-12-01 09:09:51 UTC  
> Closed at: 2021-12-01 09:09:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/945  

Small PR removing unused / commented svg generation in tests, and making sure all tests compile with TEST_WITH_SVG, and enhancing the SVG's for linestrings (diff lin/lin)

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2021-11-24 12:56:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/945#pullrequestreview-814863796  

📁 test/algorithms/overlay/get_turns_linear_linear.cpp

```diff
 672 |- 
 673 |- to_svg<ls, ls>("LINESTRING(5 0,5 5,5 0)", "LINESTRING(0 5,5 5,0 10,2 10,5 5,5 10,10 10,10 5,5 5,10 2,10 0,8 0,5 5,5 0)", "lsls101.svg");
 674 |- */
```

> Username: barendgehrels  
> Created_at: 2021-11-24 12:56:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/945#discussion_r756039479  

This was probably quite old...   
In general, we shouldn't keep this in test cases


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2021-11-26 13:55:53 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/945#pullrequestreview-816821159  

Looks good to me.

---
