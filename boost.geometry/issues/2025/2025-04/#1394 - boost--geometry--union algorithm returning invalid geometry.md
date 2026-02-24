# #1394 - boost::geometry::union algorithm returning invalid geometry [Open]

> Username: vschoech  
> Created at: 2025-04-22 15:36:54 UTC  
> Updated at: 2025-05-13 20:20:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/1394  

My `tc::geo::polygon` type is actually a **multi-polygon**, using a polygon type that is based on **int**, oriented **counter-clockwise** and **open** (not closed). I am using **boost 1.87.0**. Please consider the following example:   
  
```C++  
tc::geo::polygon<int> polygonA;  
boost::geometry::read_wkt("MULTIPOLYGON(((1353 3094,1353 2893,1852 2857,4171 2947,4909 3094,1353 3094)),((4172 2947,5707 3006,5707 3094,4909 3094,4172 2947)),((1353 2838,1353 2384,3228 2758,1852 2857,1353 2838)))", polygonA); // does not throw  
boost::geometry::is_valid(polygonA); // returns true  
  
tc::geo::polygon<int> polygonB;  
boost::geometry::read_wkt("MULTIPOLYGON(((4171 2947,3228 2758,5707 2581,5707 3006,4171 2947)))", polygonB); // does not throw  
boost::geometry::is_valid(polygonB); // returns true  
  
tc::geo::polygon<int> polygonC;  
boost::geometry::union_(polygonA, polygonB, polygonC); // does not throw  
// polygonC: MULTIPOLYGON(((4909 3094,1353 3094,1353 2893,1852 2857,4171 2947,4909 3094)),((3228 2758,5707 2581,5707 3006,5707 3094,4909 3094,4173 2948,4171 2947,3228 2758)),((1852 2857,1353 2838,1353 2384,3228 2758,1852 2857)))  
boost::geometry::is_valid(polygonC); // returns false!  
```  
  
**The union of two valid multi-polygons yields an invalid multi-polygon.**   
  
See also:  
- https://github.com/boostorg/geometry/issues/1034  
- https://github.com/boostorg/geometry/issues/1035  
- https://github.com/boostorg/geometry/issues/1395  
- https://github.com/boostorg/geometry/issues/1397

---

## Comment 1

> Username: vschoech  
> Created at: 2025-04-23 09:56:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/1394#issuecomment-2823750167  

@barendgehrels
