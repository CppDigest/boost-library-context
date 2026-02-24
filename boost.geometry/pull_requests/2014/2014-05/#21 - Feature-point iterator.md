# #21 Feature/point iterator [Merged]

> Username: mkaravel  
> Created at: 2014-05-05 06:55:07 UTC  
> Updated at: 2014-05-07 09:57:20 UTC  
> Merged at: 2014-05-07 09:57:20 UTC  
> Closed at: 2014-05-07 09:57:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/21  

implement point iterator as bidirectional iterator  
implement free functions points_front and points_back  
implement reverse iterator for points: point_reverse_iterator and free functions points_rbegin, points_rend

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-05-06 14:57:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/21#issuecomment-42313169  

Could it be possible to include specific TypeTraits instead of the whole lib?  
It's used only in the test (flatten_iterator.cpp) so probably doesn't matter but as a general suggestion...

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-05-06 19:04:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/21#issuecomment-42344891  

I replaced the generic inclusion of type traits by the specific one used (is_const).  
This was also the case not only for the unit test for flatten iterator, but also for point_iterator_type (again I needed is_const).

---
