# #104 Feature/disjoint [Closed]

> Username: mkaravel  
> Created at: 2014-07-22 12:47:25 UTC  
> Updated at: 2014-07-22 22:59:59 UTC  
> Closed at: 2014-07-22 21:17:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/104  

1. Add implementation for `disjoint` for the combinations:  
   - point/multipoint  
   - multipoint/multipoint  
   - multipoint/segment  
   - multipoint/box  
2. Add test cases for the combinations above  
3. Update support status table for `disjoint`

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-07-22 15:31:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/104#discussion_r15234726  

Currently this is O(N^2) right?  
  
A side note/thought. check_iterator_range<> doesn't look convenient and is not very readable.  
A free function could be a replacement, e.g. for_each_point_if(first, last, predicate) would have sense as a function iterating over Points for arbitrary geometry. However in this case std::find_if() could be used.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2014-07-22 17:21:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/104#issuecomment-49770890  

Please don't merge this yet.  
  
Thanks for creating - will be merged later (after adaption), But it is new functionality - not targetted to 1.56 - we should only merge bugfixes or doc enhancements, nothing new.  
  
Besides that, as Adam points out, N^2 should be avoided.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2014-07-22 17:22:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/104#issuecomment-49771110  

Please use the partition algorithm for this algorithm. It is perfectly fitted for that. In this use case (disjoint multi - all combinations)

---

## Comment 4

> Username: awulkiew  
> Created_at: 2014-07-22 17:39:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/104#discussion_r15243131  

I now see that the potentially O(N^2) function is used only for Segments and Boxes, so it'd be O(N). Maybe it'd be a good idea to reneme it to not mistakenly use it for e.g. MultiPoint/Polygon?

---

## Comment 5

> Username: mkaravel  
> Created_at: 2014-07-22 21:16:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/104#discussion_r15256897  

This is completely intentional. It is meant for points, segments and boxes only, in which case the complexity is linear.  
  
Regarding `check_iterator_range`: I agree that the name is not perfect. On the other hand I do not see the advantage of using `std::find_if`: check iterator range is calling the predicate's static apply method, and this is why I introduced this class.

---

## Comment 6

> Username: mkaravel  
> Created_at: 2014-07-22 21:17:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/104#issuecomment-49802419  

Okay, this PR will be closed and reworked for 1.57.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2014-07-22 22:53:16 UTC  
> Updated_at: 2014-07-22 22:59:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/104#discussion_r15261520  

Sorry I wasn't clear enough. Regarding the name I had multipoint_geometry in mind. This is an algorithm for Segments/Boxes, not arbitrary Geometries.  
  
Regarding the check_iterator_range, of course in the case of find_if, UnaryPredicate/function object should be passed. I also understand that the currently used approach is in-line with the rest of the BG code. I just wanted to indicate that instead of this template there could be a single line (using Boost.Range algorithm):  
  
return boost::find_if(multipoint, intersects_pred) == boost::end(multipoint)  
  
EDIT: Or a free function could be used:  
  
return detail::check_range(multipoint, predicate);

---
