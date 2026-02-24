# #1349 - Difference of gpolygons giving wrong result [Closed]

> Username: gastald88  
> Created at: 2024-11-27 16:05:42 UTC  
> Updated at: 2025-04-25 17:52:15 UTC  
> Closed at: 2025-04-25 17:52:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/1349  

I have two polygons, p1 (orange) and p2 (blue) as in figure.  
The difference of p1 and p2 returns a (wrong) empty result.  
![Image](https://github.com/user-attachments/assets/d3a29178-a800-4c90-83df-b632a53cbba1)  
  
This can be replicated in the following godbolt link:  
https://godbolt.org/z/GT7aWaWzW

---

## Comment 1

> Username: barendgehrels  
> Created at: 2024-11-27 16:50:11 UTC  
> Updated at: 2024-11-27 16:54:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/1349#issuecomment-2504349140  

Thanks for the report. I can reproduce it.  
  
```  
issue_1349<d> a: 2354350.1 b: 230426.9 union: 2355360.2 intersection: 229416.76  
diff(a-b): 0 diff(b-a): 1010.1469 sym_diff: 1010.1469   
checks: 1.1641532e-10 2124933.3 -2.6557245e-10 0 OK WRONG OK OK  
```

---

## Comment 2

> Username: barendgehrels  
> Created at: 2024-12-01 09:25:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/1349#issuecomment-2509653462  

Detailed note (mainly for myself).  
  
It's caused by the fix of #869 , a condition for difference added by me in 2021.  
  
I have to revise that part of the code. Skipping the `reverseMeaningInTurn`  is fine for all test cases, except `issue_869` which has correct areas, but is considered as invalid.  
  
In the `traversal_switch_detector.hpp` it should not be necessary change behavior for difference.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2024-12-04 17:22:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/1349#issuecomment-2518083013  

PR fixing this issue is published

---

## Comment 4

> Username: barendgehrels  
> Created at: 2025-04-25 17:52:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/1349#issuecomment-2831060357  

Fixed by #1369
