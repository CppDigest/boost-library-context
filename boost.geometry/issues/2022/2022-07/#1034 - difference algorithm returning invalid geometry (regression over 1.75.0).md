# #1034 - difference algorithm returning invalid geometry (regression over 1.75.0) [Open]

> Username: vschoech  
> Created at: 2022-07-08 12:16:44 UTC  
> Updated at: 2025-03-26 15:17:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/1034  

This problem has been with us for a while. It did not occur in 1.55.0/1.59.0, then it did occur in 1.63.0/1.64.0/1.66.0, and **was fixed in 1.67.0**, see: https://svn.boost.org/trac10/ticket/12752  
  
It then did not occur in 1.75.0, and **now occurred again in 1.79.0**.  
  
My "tc::geo::polygon" type is actually a multi-polygon, using a polygon type that is based on **int**, oriented **counter-clockwise** and **open** (not closed). I am using **boost 1.79.0**. Please consider the following example:   
  
```C++  
tc::geo::polygon<int> polygonA;  
boost::geometry::read_wkt("MULTIPOLYGON(((3232 2413,2136 2646,3232 2412,3232 2413)),((3232 2532,3232 2856,1031 2856,1031 1056,3232 1056,3232 2221,2136 2328,1032 1212,1032 1458,1032 1764,2136 2790,3232 2532)))", polygonA); // does not throw  
_ASSERT( boost::geometry::is_valid(polygonA) ); // returns true  
  
tc::geo::polygon<int> polygonB;  
boost::geometry::read_wkt("MULTIPOLYGON(((1032 2130,1032 1764,2052 2712,1032 2130)),((3234 2580,2558 2690,3234 2532,3234 2580)),((2558 2690,2136 2790,2052 2712,2136 2760,2558 2690)))", polygonB); // does not throw  
_ASSERT( boost::geometry::is_valid(polygonB) ); // returns true  
  
tc::geo::polygon<int> polygonC;  
boost::geometry::difference(polygonA, polygonB, polygonC); // does not throw  
// polygonC: MULTIPOLYGON(((3232 2413,2136 2646,3232 2412,3232 2413)),((2567 2689,3232 2581,3232 2856,1031 2856,1031 1056,3232 1056,3232 2221,2136 2328,1032 1212,1032 1458,1032 1764,1032 2130,2052 2712,2136 2790,2567 2689),(1032 1764,2053 2713,2052 2712,1032 1764)))  
boost::geometry::is_valid(polygonC); // returns false!  
```  
  
**The difference of two valid multi-polygons yields an invalid multi-polygon.**   
  
See also:  
- https://github.com/boostorg/geometry/issues/602  
- https://github.com/boostorg/geometry/issues/1035

---

## Comment 1

> Username: barendgehrels  
> Created at: 2022-10-19 15:17:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/1034#issuecomment-1284180101  

Sorry for the delay in checking this.  
  
Thanks for the details in the tickets, as always.  
  
I can reproduce it, and I am looking into it. If I make all coordinates 10 times larger, the output looks similar but is valid. My theory is that because of the intersection points (which are rounded to the integer grid), in some cases invalid parts can appear. I will look further into this next week, but this is not easy to fix.  
  
This is also why it occurs again now, because we round coordinates a bit differently than before. That fixed some issues, but indeed might change other issues as well.  
  
Most probably the same happens your issue #1035 (but I did not try that one today).

---

## Comment 2

> Username: vschoech  
> Created at: 2023-08-28 11:36:52 UTC  
> Updated at: 2023-08-28 13:41:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/1034#issuecomment-1695540160  

:warning: Still reproducible in the same way in boost 1.83.0.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2024-11-25 18:43:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/1034#issuecomment-2498777622  

Hi @vschoech ,  
Still the case in `1.87` beta, unfortunately. And it's really hard to fix - that's why it takes so long. Sorry...

---

## Comment 4

> Username: vschoech  
> Created at: 2025-03-26 15:17:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/1034#issuecomment-2754794109  

⚠️ Indeed still reproducible in boost 1.87.0.
