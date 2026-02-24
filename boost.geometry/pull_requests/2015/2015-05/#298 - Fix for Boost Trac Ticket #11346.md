# #298 Fix for Boost Trac Ticket #11346 [Merged]

> Username: mkaravel  
> Created at: 2015-05-27 06:23:44 UTC  
> Updated at: 2015-06-01 20:53:07 UTC  
> Merged at: 2015-05-30 16:35:11 UTC  
> Closed at: 2015-05-30 16:35:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/298  

**Problem:** segment iterator does not work with point iterators that return a value instead of a reference when dereferenced.  
  
**Fix:** check the if the reference type of the point iterator is indeed a reference or not and use a pointing_segment or a segment as the value type of the segment iterator, respectively.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-05-27 08:07:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/298#issuecomment-105811228  

Thanks. I'm OK with merging. Is it possible to add a unit test for this case? We have already some unit tests based on pointers (but only for a few cases). For example void test_pointer_version() in intersection.cpp

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-05-27 08:44:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/298#issuecomment-105826188  

@barendgehrels I do not see how what you suggest is relevant. I am not saying that your suggestion is bad, and indeed, we should add test cases like the one you suggestion, but for another reason: to make sure that pointers to points are treated correctly.  
  
The problem that this patch tries to fix is the following: suppose you have a linestring and say `it` be an iterator to the points of this linestring. Typically `*it` returns a reference or a const reference to the point of the linestring it points to. However, it is possible that `*it` returns a copy of the point, and this is what was causing the problem.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2015-05-27 08:50:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/298#issuecomment-105828233  

The suggestion is: if we have a fix, we create a unit testcase too, as we usually do. I think that suggestion is relevant ;-)  
I actually thought the problem at hand did use pointers, but that is apparently not the case for this fix.  
So the unit test might need another test geometry simulating the copy behaviour.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-05-27 08:54:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/298#issuecomment-105828921  

Yes, I agree with the concept.  
I am right now in the process of adding test cases to point and segment iterator for linestrings of point pointers. It was a good suggestion.  
I will try to add another test case for this bug. It will be a bit more complicated though....

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2015-05-27 08:58:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/298#issuecomment-105829824  

OK - if you want to merge this PR before the UT, OK for me too, no problem.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2015-05-27 11:04:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/298#issuecomment-105871765  

Developing a unit test would require to implement a custom iterator and then adapting a geometry, e.g. linestring using it. Btw, such geometry could be tested in many tests. In general, we could have many other test geometries. So if you're going to implement it, please put the implementation in a separate header file somewhere in the test directory.  
  
I'm of course ok with merging.

---

## Comment 7

> Username: mkaravel  
> Created_at: 2015-05-27 11:32:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/298#issuecomment-105878658  

@awulkiew Exactly, and this is what takes a bit more time.  
I already had in mind to put my test geometry in a separate header, under `test/test_geometries`.  
I am not sure how to name it though (I am working on a linestring...).

---

## Comment 8

> Username: mkaravel  
> Created_at: 2015-05-28 06:48:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/298#issuecomment-106198281  

@barendgehrels I have added test cases for both the point and the segment iterator. In both cases I now test geometries whose point type is a pointer to a point, as well as test geometries whose range iterators return values instead of references. See commit 424ba74.  
@awulkiew I have started the implementation of geometries whose range iterator returns points by value rather then by reference. Up to now multipoints and linestrings are supported (were the easier to do), but we can extend them. These geometries are in a separate file and as such they can be used in other unit tests. See commit cf9a691.

---

## Comment 9

> Username: mkaravel  
> Created_at: 2015-05-28 06:50:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/298#issuecomment-106198476  

@barendgehrels @awulkiew AFAIU you are both okay to merge this PR.  
I will leave the day to take a look at the changes in the unit tests and tomorrow I will merge it (unless there are opinions to the opposite).  
  
Thanks a lot for your comments.

---
