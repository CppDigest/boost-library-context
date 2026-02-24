# #854 Update line_interpolate tests [Merged]

> Username: vissarion  
> Created at: 2021-05-24 14:53:35 UTC  
> Updated at: 2021-06-30 14:07:57 UTC  
> Merged at: 2021-05-25 10:18:18 UTC  
> Closed at: 2021-05-25 10:18:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/854  



---

## Comment 1

> Username: awulkiew  
> Created_at: 2021-05-24 19:18:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/854#issuecomment-847275415  

Thanks! Do you know why have the result changed?

---

## Comment 2

> Username: vissarion  
> Created_at: 2021-05-25 07:56:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/854#issuecomment-847640994  

> Thanks! Do you know why have the result changed?  
  
Maybe the changes in line_interpolate umbrella strategies. Line interpolate tests that contain in the output the last point of the input linestring are not robust since getting that point in the computation depends on computations of distances and summation of distances. I made the tests more robust by removing or changing all those case.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2021-05-25 10:18:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/854#issuecomment-847745141  

Ok, thanks!

---
