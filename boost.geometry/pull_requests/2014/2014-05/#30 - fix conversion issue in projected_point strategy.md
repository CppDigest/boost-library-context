# #30 [strategies] fix conversion issue in projected_point strategy: [Merged]

> Username: mkaravel  
> Created at: 2014-05-14 23:46:45 UTC  
> Updated at: 2014-05-16 11:24:08 UTC  
> Merged at: 2014-05-16 11:24:08 UTC  
> Closed at: 2014-05-16 11:24:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/30  

Problem:  
when p1 (2nd argument of apply) has float as coordinate type, then  
the result of the differences u-p1 and w-p1 is a double (because  
the calculation type is double), which then is implicitly converted  
to float (because the result of the difference is implicitly  
converted to the type of the second operand), which means that we  
may loose precision.  
  
Solution:  
convert p1 to the correct point type (fp_point_type), and then  
perform the subtractions (p1 is anyways converted later on, so  
just do then conversion a bit earlier).  
  
Note:  
This problem was generated as a warning on VS2010, and also appears  
as a warning in clang++ and g++ if the -Wconversion option is used.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-05-15 21:44:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/30#issuecomment-43269063  

Barend are you ok with the merge?  
Or maybe we should modify the arithmetic functions (subtract_point in this case) to use the more precise coordinate_type for calculation and then perform numeric_cast?

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-05-15 22:12:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/30#issuecomment-43271578  

I think that these are two independent things. The strategy should perform in principle all computations using the calculation type, and in that respect, the fix I propose is still valid.  
Arithmetic functions should also be more "careful" with respect to what they return, so Adam's point is valid too (but as I see it indepedent from/orthogonal to the strategy).

---
