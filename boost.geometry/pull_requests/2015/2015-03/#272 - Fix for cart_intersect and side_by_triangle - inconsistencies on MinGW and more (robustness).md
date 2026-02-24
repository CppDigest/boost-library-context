# #272 Fix for cart_intersect and side_by_triangle - inconsistencies on MinGW and more (robustness) [Merged]

> Username: awulkiew  
> Created at: 2015-03-14 03:35:15 UTC  
> Updated at: 2015-04-09 01:15:41 UTC  
> Merged at: 2015-03-22 13:44:33 UTC  
> Closed at: 2015-03-22 13:44:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/272  

This PR is an attempt to fix the inconsistent results of sides and segment's intersection calculation on various platforms. For now I tested it on msvc10, ~~msvc13~~ msvc12 and mingw-w64 4.9.1 on which different results was generated before.  
  
Before, for floating-point coordinates, the results of determinant (side_by_triangle, cramer's rule) was compared with 0, scaling the machine epsilon by the greatest absolute of compared value. This wasn't a good solution since for nearly collinear segments the results are very small. For all of them factor 1 was always used (raw epsilon) no matter how long were the segments, what coordinates were used, so how great was the error. Furthermore on various platforms different instructions are generated for the determinant expression. For some instructions sets the error of the result is proportional to the input (so a difference of coordinates). The code added in this PR scales the epsilon by the greatest absolute value of the difference of points' coordinates passed into the determinant formula. For this purpose the support for EqualsPolicy was added which can be passed to `math::detail::equals_by_policy()` where is used to retrieve the epsilon factor.  
  
This PR is related to this one: https://github.com/boostorg/geometry/pull/259, which was the previous attempt and this ticket: https://svn.boost.org/trac/boost/ticket/8379.  
  
As a side effect some of the tests (e.g. difference) previously failing now passes.  
  
This PR doesn't solve all inconsistencies, there are tests which are still failing (e.g. get_turns, enabled with a macro) but now they're doing it in a more consistent way, i.e. similar ones generates the same, wrong output.  
  
NOTE: The PR: https://github.com/boostorg/geometry/pull/267 is a part of this one so it should be merged first.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-03-15 01:56:13 UTC  
> Updated_at: 2015-03-15 01:56:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/272#issuecomment-80795629  

The tests pass also on Linux GCC 4.8.2 and Clang 3.4.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-03-22 10:18:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/272#issuecomment-84580267  

This is a larger changelist. I'm OK with this. I'm curious how it would work, also for in case areal/areal overlay operations do not use integer. But you don't have to test that, I will do that later. I'm OK with merging this.

---
