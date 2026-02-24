# #129 Feature/disjoint 1.57 [Merged]

> Username: mkaravel  
> Created at: 2014-08-08 22:25:19 UTC  
> Updated at: 2014-11-05 06:29:45 UTC  
> Merged at: 2014-11-04 21:25:35 UTC  
> Closed at: 2014-11-04 21:25:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/129  

This is a follow-up of PR #104.  
This PR contains the following contributions:  
1. Adds implementation for disjoint for the combinations:  
   - point/multipoint  
   - multipoint/multipoint  
   - multipoint/segment  
   - multipoint/box  
2. Add test cases for the combinations above

---

## Comment 1

> Username: mkaravel  
> Created_at: 2014-08-08 22:30:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/129#issuecomment-51665017  

Follow up on Adam's comment about the name of `multipoint_geometry`: this has been renamed to `multipoint_constant_size_geometry` to indicate that it refers to points, segments and boxes only.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2014-08-08 22:39:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/129#issuecomment-51665555  

If I remember correctly this is an implementation for MultiPoint vs Box/Segment? If yes, couldn't it just be named multipoint_box_or_segment? A Linestring or Polygon might be of "constant size" as well.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2014-08-08 22:45:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/129#issuecomment-51665970  

Follow up on Barend's comment about using `partition`.  
  
The partition-based approach has been implemented and it is currently guarded with the macro `BOOST_GEOMETRY_DISJOINT_USE_PARTITION`. The default implementation is still using `std::sort` and `std::binary_search`, as it turned out to be faster than the partition-based approach.  
  
Here are the performance results.  
I considered two multi-points with points randomly distributed in the square `[0,1]x[0,1]`.  
The following table shows the timings in seconds.  
`N` stands for the number of points in each multi-point (so both are of the same size).  
In all cases the two multi-points are disjoint, which is the worst case scenario for both approaches.  
The performance test program was compiled with `clang++ -O3 -DNDEBUG -std=c++11 -stdlib=libc++`.  
Boost.Timer has been used for timings.  
  
| N | sort-based | partition-based |  
| :-: | --: | --: |  
| 10^2 | 0.000044 | 0.000090 |  
| 10^3 | 0.000214 | 0.000972 |  
| 10^4 | 0.002094 | 0.006980 |  
| 10^5 | 0.033148 | 0.066813 |  
| 10^6 | 0.495144 | 0.864893 |  
| 10^7 | 8.034012 | 13.112425 |

---

## Comment 4

> Username: mkaravel  
> Created_at: 2014-08-08 22:51:16 UTC  
> Updated_at: 2014-08-08 22:52:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/129#issuecomment-51666427  

@awulkiew The implementation is used for points as well. It seemed too much to write something like `multipoint_point_or_box_or_segment`.  
  
I really do not see linestrings and polygons as constant-sized objects.  
  
For points, segments and boxes it only suffices to describe O(1) points to define them.  
  
For linestrings and polygons you always need Theta(N) points to define them, and even though N can be small (or constant-size if you want), it is still a variable. A triangle is a constant size polygon; a rectangle also. But when referring to linestrings or polygons in a generic way, they are not of constant size.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2014-08-08 22:58:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/129#issuecomment-51666886  

Regarding the Adam's comment about `check_iterator_range`, this is not considered in this PR, as it refers to an algorithm detail of more general scope than `disjoint`, and merits a separate discussion and PR.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2014-08-09 00:25:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/129#issuecomment-51671220  

@mkaravel the term "constant size" is just ambiguous to me, it may mean many various things, as you wrote.  
  
I had a few ideas regarding the name but came up with something different (btw, `multipoint_point_or_box_or_segment` in only 1 character longer than `multipoint_constant_size_geometry`).  
  
Correct me if I'm wrong, basically this algorithm should work for all Multi-Geometries, in other words ranges of Geometries. Not only for MultiPoints. And in fact, in `linear_segment_or_box.hpp` there is already an algorithm `disjoint_multirange_segment_or_box` added by you which does the same thing. Or am I missing something? Wouldn't this work as well in this case?  
  
I propose to give it some generic name like: `disjoint_range`, `disjoint_multirange` or `disjoint_multi` since as you said it just linearly checks the Range of Geometries (no matter what's passed). Of course assuming that those names aren't picked yet.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2014-10-08 23:37:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/129#issuecomment-58444770  

Ping. Does `disjoint_multirange_segment_or_box` do the same thing as `multipoint_constant_size_geometry`? If the answer is yes, what do you think about unifying them?

---

## Comment 8

> Username: mkaravel  
> Created_at: 2014-10-14 09:08:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/129#issuecomment-59011053  

@awulkiew I will take a look at it ASAP.

---

## Comment 9

> Username: mkaravel  
> Created_at: 2014-10-14 14:06:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/129#issuecomment-59049698  

@awulkiew Done. The functionality was indeed the same. I have factored-out the common code and I have simplified a bit the `disjoint_segment_areal` implementation.  
  
AFAIC this PR is ready for merging. One thing that we need to take care of are the conflicts that will almost certainly created when trying to merge PR #159 (once the discussion for that PR is over).

---

## Comment 10

> Username: awulkiew  
> Created_at: 2014-10-14 15:08:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/129#issuecomment-59061254  

Ok, great. Let's wait for Barend then.

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2014-10-14 18:40:55 UTC  
> Updated_at: 2014-11-04 13:16:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/129#discussion_r18849415  

This should not be done in production code. Please provide one solution.

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2014-10-14 18:43:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/129#issuecomment-59095354  

Did not look it up, but are these combinations  
  
```  
point/multipoint  
multipoint/multipoint  
multipoint/segment  
multipoint/box  
```  
  
new for 1.57?  
If yes, we should postpone this - it is new functionality...

---

## Comment 13

> Username: mkaravel  
> Created_at: 2014-10-14 20:14:00 UTC  
> Updated_at: 2014-11-04 13:16:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/129#discussion_r18855044  

Will remove the guarded code.

---

## Comment 14

> Username: mkaravel  
> Created_at: 2014-10-14 20:15:10 UTC  
> Updated_at: 2014-10-14 20:19:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/129#issuecomment-59108921  

The answer is yes. ~~In fact the newly added combinations are `multipoint/geometry`.~~  
No problem with waiting for 1.58.

---
