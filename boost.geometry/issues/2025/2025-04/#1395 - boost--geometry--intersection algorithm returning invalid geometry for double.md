# #1395 - boost::geometry::intersection algorithm returning invalid geometry for double [Open]

> Username: vschoech  
> Created at: 2025-04-24 07:33:16 UTC  
> Updated at: 2025-05-13 20:19:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/1395  

My `tc::geo::polygon` type is actually a **multi-polygon**, using a polygon type that is based on **double**, oriented **counter-clockwise** and **open** (not closed). I am using **boost 1.87.0**.  
  
We compile with Microsoft Visual C++ 2022 for x86 (Win32), using the `/fp:fast` flag. The issue is also reproducible when compiling for the x64 platform.  
  
Please consider the following example:   
  
```C++  
tc::geo::polygon<double> polygonA;  
boost::geometry::read_wkt("MULTIPOLYGON(((2601.6180555555557 201.34722222222223,2601.6180555555557 582.8472222222223,372.41666666666663 1065.3993055555554,372.41666666666663 201.34722222222223,2601.6180555555557 201.34722222222223)))", polygonA); // does not throw  
boost::geometry::is_valid(polygonA); // returns true  
  
tc::geo::polygon<double> polygonB;  
boost::geometry::read_wkt("MULTIPOLYGON(((2482.777777777778 201.34722222222223,2400.270833333333 1421.5416666666665,1983.1944444444443 1421.5416666666665,1900.6875 201.34722222222223,2482.777777777778 201.34722222222223)))", polygonB); // does not throw  
boost::geometry::is_valid(polygonB); // returns true  
  
tc::geo::polygon<double> polygonC;  
boost::geometry::intersection(polygonA, polygonB, polygonC); // does not throw  
// polygonC: MULTIPOLYGON(((2482.777777777778 201.34722222222223,2482.777777777778 201.34722222222217,2454.8330485880297 614.621565835736,1936.2231963642907 726.8843097381582,1900.6875 201.34722222222223,2482.777777777778 201.34722222222223)))  
boost::geometry::is_valid(polygonC); // returns false!  
```  
  
**The intersection of two valid multi-polygons yields an invalid multi-polygon.**   
  
See also:  
- https://github.com/boostorg/geometry/issues/1034  
- https://github.com/boostorg/geometry/issues/1035  
- https://github.com/boostorg/geometry/issues/1394  
- https://github.com/boostorg/geometry/issues/1397

---

## Comment 1

> Username: vschoech  
> Created at: 2025-04-24 07:39:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/1395#issuecomment-2826666737  

@barendgehrels
