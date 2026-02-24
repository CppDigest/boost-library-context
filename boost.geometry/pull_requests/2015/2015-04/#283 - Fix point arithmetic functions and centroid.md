# #283 Fix point arithmetic functions and centroid [Merged]

> Username: awulkiew  
> Created at: 2015-04-24 15:31:01 UTC  
> Updated at: 2015-04-25 22:55:15 UTC  
> Merged at: 2015-04-25 22:55:12 UTC  
> Closed at: 2015-04-25 22:55:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/283  

The intention of this PR is to fix this ticket: https://svn.boost.org/trac/boost/ticket/11236  
  
In this particular case, at the end of centroid calculation the resulting point is moved using the origin (taken from the input geometry). The point arithmetic functions internally was calculating the results using always the type of the second operand (in one of the STL binary predicates like `std::plus`). In this particular case the resulting Point is using coordinates of type `double` and the input Polygon coordinates of type `int`. `bg::add_point()` internally calls `std::plus<int>` which truncates the `double` value before addition. Ths PR changes it by using the more precise type.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-04-24 18:05:26 UTC  
> Updated_at: 2015-04-25 14:37:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/283#discussion_r29071266  

Abbreviations used (Dst, dest) are not the same.  
While you are at it, you might change I to Index and make type std::size_t  
  
What is C ?

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-04-24 18:07:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/283#issuecomment-96017394  

For the rest I'm OK

---

## Comment 3

> Username: awulkiew  
> Created_at: 2015-04-25 11:13:50 UTC  
> Updated_at: 2015-04-25 14:37:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/283#discussion_r29099884  

My primary goal was was to fix the issue so I've taken the names that were already used in this code. So `C` is a type of a value passed as the second argument to the `XXX_value()` functions. But sure, I'll fix the names.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2015-04-25 11:22:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/283#issuecomment-96177262  

Thanks!  
  
I now saw that a type of a parameter taken by `XXX_value()` functions is always `coordinate_type<Point>`. So my change wasn't needed in this case. So currently my fix doesn't modify the behavior of the function, but we could consider taking an arbitrary type as the second parameter. Then the intermediate result would be calculated using the most precise type. Though this is probably not needed in general because those functions aren't used in the library like this, at least for now.

---
