# #1035 - difference algorithm returning invalid geometry (regression over 1.75.0) [Open]

> Username: vschoech  
> Created at: 2022-07-08 12:35:46 UTC  
> Updated at: 2025-03-27 10:04:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/1035  

This problem has been with us for a while. I've seen it in 1.59.0/1.66.0, and it **was fixed in 1.67.0**, see: https://svn.boost.org/trac10/ticket/11676  
  
It then did not occur in 1.75.0, and **now occurred again in 1.79.0**.  
  
My "tc::geo::polygon" type is actually a multi-polygon, using a polygon type that is based on **int**, oriented **counter-clockwise** and **open** (not closed). I am using **boost 1.79.0**. Please consider the following example:   
  
```C++  
tc::geo::polygon<int> polygonA;  
boost::geometry::read_wkt("MULTIPOLYGON(((2974 352,2940 336,2907 334,2907 317,2974 317,2974 352)),((2940 354,2957 361,2907 361,2907 353,2940 354)))", polygonA); // does not throw  
_ASSERT( boost::geometry::is_valid(polygonA) ); // returns true  
  
tc::geo::polygon<int> polygonB;  
boost::geometry::read_wkt("MULTIPOLYGON(((576 330,576 312,777 348,576 330)),((2968 349,2940 342,2902 334,2940 336,2968 349)),((3612 516,3276 498,2968 349,3276 432,3612 444,3612 516)),((2902 334,2598 324,2262 438,1926 414,1590 384,1248 354,1590 372,1926 384,2262 402,2598 276,2902 334)),((1248 354,912 372,777 348,912 360,1248 354)))", polygonB); // does not throw  
_ASSERT( boost::geometry::is_valid(polygonB) ); // returns true  
  
tc::geo::polygon<int> polygonC;  
boost::geometry::difference(polygonA, polygonB, polygonC); // does not throw  
// polygonC: MULTIPOLYGON(((2940 354,2957 361,2907 361,2907 353,2940 354)),((2974 351,2974 352,2967 348,2968 349,2974 351)),((2940 336,2907 334,2907 317,2974 317,2974 351,2968 349,2940 336)))  
boost::geometry::is_valid(polygonC); // returns false!  
```  
  
**The difference of two valid multi-polygons yields an invalid multi-polygon.**   
  
See also:  
- https://github.com/boostorg/geometry/issues/602  
- https://github.com/boostorg/geometry/issues/1034

---

## Comment 1

> Username: vschoech  
> Created at: 2023-08-28 13:19:01 UTC  
> Updated at: 2023-08-28 13:42:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/1035#issuecomment-1695687763  

:warning: Still reproducible in the same way in boost 1.83.0.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2024-11-25 18:48:49 UTC  
> Updated at: 2024-11-25 18:49:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/1035#issuecomment-2498788292  

Same here, not fixed in `1.87`  
  
For double all fine. For integer `diff1(a-b) false`  
  
(Report for double:  
```  
issue_1035<d> a: 1819 b: 79785 union: 81597.743 intersection: 6.2571429 diff(a-b): 1812.7429 diff(b-a): 79778.743 sym_diff: 81591.486    checks: -1.9895197e-11 1.7735147e-11 4.3655746e-11 0 OK OK OK OK  
```  
)

---

## Comment 3

> Username: vschoech  
> Created at: 2025-03-27 10:04:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/1035#issuecomment-2757464617  

⚠️ I can confirm that this is still reproducible in the same way in boost 1.87.0.
