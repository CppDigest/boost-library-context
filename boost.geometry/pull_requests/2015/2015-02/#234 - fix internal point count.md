# #234 [strategies][cartesian][buffer][point_circle] fix internal point count [Merged]

> Username: mkaravel  
> Created at: 2015-02-26 15:31:23 UTC  
> Updated at: 2015-02-26 17:57:06 UTC  
> Merged at: 2015-02-26 17:56:05 UTC  
> Closed at: 2015-02-26 17:56:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/234  

for small input values (less than 3): when the input point count is less  
than 3, set the internal point count to 3; this is important for generating  
valid polygons;

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-02-26 17:56:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/234#issuecomment-76228990  

Thanks

---
