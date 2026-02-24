# #300 Disable buffer test cases involving empty geometries [Closed]

> Username: mkaravel  
> Created at: 2015-06-01 09:19:26 UTC  
> Updated at: 2015-06-03 10:15:37 UTC  
> Closed at: 2015-06-01 20:51:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/300  

Some buffer test cases failed when `BOOST_GEOMETRY_ENABLE_ACCESS_DEBUGGING`. These test cases involve testing empty geometries (e.g., `LINESTRING()`, `POLYGON(())`, etc.).  
This PR disables these test cases.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-06-01 12:41:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/300#issuecomment-107427249  

Let me just say that this should be considered as an intermediate solution.  
What I prefer to have is to have `buffer()` throw an exception if the input is empty, and then the unit test should basically check that the exception is properly thrown.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-06-01 12:41:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/300#issuecomment-107427406  

So the problem is what should be the result of a buffer() for an empty geometry input. We have at least two possibilities:  
1. generate an empty geometry (MultiPolygon containing 0 points)  
2. throw and exception  
Both solutions are reasonable. Do I understand correctly that the 1. is the original behavior?  
With that said this PR doesn't solve the problem and without the debugging enabled the problem is not manifested so the tests passes (though on some compilers there are warnings about the use of uninitialized variables).

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-06-01 12:43:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/300#issuecomment-107427770  

@awulkiew As I wrote in my comment, I prefer solution 2 (among the two you suggest).  
Barend?

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2015-06-01 16:53:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/300#issuecomment-107635736  

A buffer of an empty geometry (which is valid) should not throw an exception. So it should return an empty polygon. And it did. So behaviour was OK.  
  
So please close this PR - it should be solved differently.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-06-01 20:51:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/300#issuecomment-107703854  

@barendgehrels Okay, fair enough. I will try to think of another way to solve the problem then.

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2015-06-03 10:15:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/300#issuecomment-108287428  

Buffer (boost::geometry::buffer) now returns empty if input is empty.  
  
The problems where in the test case (testing boost::geometry::buffer_inserter), which now assigns the envelope with inverse if input is empty (as it was before). I did not get the error message of the regression, but it restores previous behaviour so it is expected that that is OK now.  
  
There was actually no unit test for the boost::geometry::buffer with strategies (wrapping buffer_inserter), so I added that and check for empty geometries too

---
