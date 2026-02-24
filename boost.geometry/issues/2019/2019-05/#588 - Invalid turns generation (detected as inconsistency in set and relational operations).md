# #588 - Invalid turns generation (detected as inconsistency in set and relational operations). [Open]

> Username: awulkiew  
> Created at: 2019-05-07 22:37:10 UTC  
> Updated at: 2020-01-10 00:19:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/588  

@barendgehrels I've noticed that some operations became inconsistent after recent changes in set operations (with VS2015, msvc-14.0, x86). Consider:  
```  
red = POLYGON((5 0,7 10,0 15,10 15,15 25,20 15,30 15,22 10,25 0,15 5,5 0))  
green = POLYGON((15 0,17 10,10 15,20 15,25 25,30 15,40 15,32 10,35 0,25 5,15 0))  
blue = MULTIPOLYGON(((10 15,15 25,20 15,10 15)),((10 15,17 10,15.90909090909091 4.545454545454546,15 5,5 0,7 10,0 15,10 15)),((23.69565217391304 4.347826086956522,25 0,20 2.5,23.69565217391304 4.347826086956522)))  
```  
![image](https://user-images.githubusercontent.com/1226951/57336100-0ef24d80-7125-11e9-9b72-2c2fd9c4d64e.png)  
where:  
```  
bg::difference(red, green, blue)  
```  
`blue` should therefore be within `red`:  
```  
assert(bg::within(blue, red) == true)  
```  
but it is not. Here it is how it looks like with turns calculated internally in `within` or `relation`:  
  
![image](https://user-images.githubusercontent.com/1226951/57336227-75776b80-7125-11e9-82e7-8b30fe93f071.png)  
  
The lower right `blue` triangle has the following points:  
```  
{23.695652173913043, 4.3478260869565215}  
{25.000000000000000, 0.00000000000000000}  
{20.000000000000000, 2.5000000000000000}  
{23.695652173913043, 4.3478260869565215}  
```  
the lower right arm of the `red` star is formed from the points:  
```  
{22.000000000000000, 10.000000000000000}  
{25.000000000000000, 0.00000000000000000}  
{15.000000000000000, 5.0000000000000000}  
```  
and the corresponding turns are:  
```  
0: touch u/i at {25.000000000000000, 0.00000000000000000}  
1: crosses i/u at {20.000001500000678, 2.5000007500003396}  
2: crosses u/i at {23.695651652173520, 4.3478258260867602}  
```  
(note: turns indexes are different than in `within`)  
  
So it looks like at `{25, 0}` (turn 0) the blue triangle goes outside the red and then crosses it from the outside at `{20.000001500000678, 2.5000007500003396}` (turn 1), goes inside and then at `{23.695651652173520, 4.3478258260867602}` (turn 2) outside again. Also the coordinates looks consistent with topology since `{20.000001500000678, 2.5000007500003396}` is above and on the right of `{20.000000000000000, 2.5000000000000000}` and `{23.695651652173520, 4.3478258260867602}` is below and on the left of `{23.695652173913043, 4.3478260869565215}`.  
  
However this should not be the case. All turns for this triangle should be detected as lying on the edges. And this is not some numerical floating point error. Consider the `POINT(20.0 2.5)`. It's exactly in the half of the `SEGMENT(25 0, 15 5)` but the turn point calculated for this point is `{20.000001500000678, 2.5000007500003396}`. The error is huge.  
  
Btw, turns in relate are calculated like this: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/relate/areal_areal.hpp#L225  
  
Or am I missing something?  
  
EDIT:  
Is this a bug?  
Should turns be generated in a different way now?  
Is this low accuracy expected?  
  
Note that if such low accuracy was expected (e.g. was a side effect of your changes) we would have to for instance compare all of the points (for equality) in the library WRT to this accuracy.

---

## Comment 1

> Username: barendgehrels  
> Created at: 2019-05-08 07:27:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/588#issuecomment-490379659  

Thanks for the report. As far as I know, for within calculations rescaling is not used? Then it might be influenced by my recent changes indeed.  
It might be that there is a start turn added?  
Do you happen to have a test project and can we bisect it?

---

## Comment 2

> Username: barendgehrels  
> Created at: 2019-05-08 10:07:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/588#issuecomment-490428951  

It looks indeed like the start turn can cause this, and also some false positives about validity.  
Looking at it right now, hope to send a PR soon.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2019-05-08 19:10:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/588#issuecomment-490613106  

I can exclude the start-turns for non-overlay operations, and I will.  
However, I added also your testcase to the within unit test but it still fails. Are you sure it should return really "within" (so: true)?  
  
About the calculated turn, that (resulting coordinates) is all not affected (as far as I'm aware) by my changes. I only changed things in turns, not in the calculations themselves.

---

## Comment 4

> Username: awulkiew  
> Created at: 2019-05-08 20:56:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/588#issuecomment-490647845  

> I can exclude the start-turns for non-overlay operations, and I will.  
  
Ok but will then set operations and relational operations be consistent with each other?  
  
> Are you sure it should return really "within" (so: true)?  
  
Yes. C = A \ B <=> C in A  
  
> that (resulting coordinates) is all not affected (as far as I'm aware) by my changes  
  
I only checked that the result is different in 1.69 and 1.70. It may be something different. I'll try to find the specific commit when the behavior changes.  
  
> I only changed things in turns, not in the calculations themselves.  
  
Turns generated now are different than the ones before. It's not only the coordinates.

---

## Comment 5

> Username: awulkiew  
> Created at: 2019-05-08 21:02:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/588#issuecomment-490649800  

Btw, my test program is more or less:  
```  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/geometries.hpp>  
#include <iostream>  
  
template <typename Geometry>  
inline void read_wkt(std::string const& wkt, Geometry & geometry)  
{  
    namespace bg = boost::geometry;  
    bg::read_wkt(wkt, geometry);  
    bg::correct(geometry);  
}  
  
int main()  
{  
    namespace bg = boost::geometry;  
    typedef bg::model::point<double, 2, bg::cs::cartesian> point;  
    typedef bg::model::polygon<point> polygon;  
    typedef bg::model::multi_polygon<polygon> mpolygon;  
  
    polygon red, green;  
  
    read_wkt("POLYGON((5 0,7 10,0 15,10 15,15 25,20 15,30 15,22 10,25 0,15 5,5 0))", red);  
    read_wkt("POLYGON((15 0,17 10,10 15,20 15,25 25,30 15,40 15,32 10,35 0,25 5,15 0))", green);  
  
    mpolygon blue;  
    bg::difference(red, green, blue);  
  
    bool w1 = bg::within(blue, red);  
    std::string s1 = bg::relation(blue, red).str();  
  
    std:: cout << (w1 == true ? "OK" : "WRONG") << std::endl;  
    std:: cout << s1 << std::endl;  
  
    std::cout << std::setprecision(16);  
    std::cout << bg::wkt(red) << std::endl;  
    std::cout << bg::wkt(green) << std::endl;  
    std::cout << bg::wkt(blue) << std::endl;  
}  
```

---

## Comment 6

> Username: barendgehrels  
> Created at: 2019-05-09 21:09:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/588#issuecomment-491067335  

Thanks for your testprogram!  
  
> Ok but will then set operations and relational operations be consistent with each other?  
  
Good point - I have to check that indeed. Will come back to this, but cannot do that right now.

---

## Comment 7

> Username: barendgehrels  
> Created at: 2019-05-15 10:59:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/588#issuecomment-492607717  

I checked the cases failing with starting points and I'm able to solve them differently now.  
Created PR #589 (it disables two cases failing, but locally I've changes still to be committed which will fix them).  
Closing this for now.

---

## Comment 8

> Username: awulkiew  
> Created at: 2019-05-15 20:58:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/588#issuecomment-492821431  

I've checked the case reported in this issue with your branch (`barendgehrels:bugfix/disable_start_turns`) but the result didn't change, still the same turns are generated. I'm going to find the exact commit which introduced the regression to be sure what is the cause. Blind fixing is probably not a good strategy. ;)

---

## Comment 9

> Username: awulkiew  
> Created at: 2019-05-15 21:48:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/588#issuecomment-492836234  

@barendgehrels I found the cause. It seems my commit introduced this regression and your initial thought about rescaling was correct. Here is the commit: https://github.com/boostorg/geometry/commit/5ea1abcedc301bcacf6fcff157b4cd88184eab2f  
  
Reverting it fixes the problem however this was a change made in this PR: https://github.com/boostorg/geometry/pull/550 so it needs re-testing in case it was important and not only made for consistency. I don't remember exactly.  
  
The issue is caused by rescaling. It also affects set operations. I checked the result of the difference of `red - blue` and it contains these invalid turns like `POINT(20.000001500000678 2.5000007500003396)`:  
```  
MULTIPOLYGON(((10 15,20 15,30 15,22 10,23.69565165217352 4.34782582608676,20.00000150000068 2.50000075000034,15.90909090909091 4.545454545454546,17 10,10 15)))  
```

---

## Comment 10

> Username: awulkiew  
> Created at: 2019-05-15 23:09:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/588#issuecomment-492856840  

Reverting https://github.com/boostorg/geometry/commit/5ea1abcedc301bcacf6fcff157b4cd88184eab2f causes the following `difference` cases to fail as it was before https://github.com/boostorg/geometry/pull/550 was merged:  
```  
algorithms_difference  
difference: mysql_23023665_6_b not valid Multi-polygon has intersecting interiors type: d  
difference: mysql_23023665_6_s not valid Multi-polygon has intersecting interiors type: d  
  
algorithms_difference_multi  
difference: mysql_21965285_b_s not valid Multi-polygon has intersecting interiors type: d  
```  
  
So I'm not sure what is the correct course of action here.

---

## Comment 11

> Username: awulkiew  
> Created at: 2019-05-15 23:18:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/588#issuecomment-492858763  

The above means this issue is still on the table.

---

## Comment 12

> Username: awulkiew  
> Created at: 2019-05-16 14:09:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/588#issuecomment-493083113  

@barendgehrels I'm ok with leaving it as it is for now, with this case failing, this issue open (as a reminder) and waiting for the disabling of rescaling in the whole library consistently.

---

## Comment 13

> Username: barendgehrels  
> Created at: 2019-05-16 16:44:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/588#issuecomment-493144608  

OK, is fine for me - I probably will include this case in my tests for removing the rescalnig.

---

## Comment 14

> Username: awulkiew  
> Created at: 2020-01-10 00:19:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/588#issuecomment-572816730  

Just an update. This issue is in 1.72.
