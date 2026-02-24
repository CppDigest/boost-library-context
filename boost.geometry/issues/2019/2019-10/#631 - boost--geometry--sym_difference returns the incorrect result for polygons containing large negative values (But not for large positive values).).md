# #631 - boost::geometry::sym_difference returns the incorrect result for polygons containing large negative values (But not for large positive values).) [Open]

> Username: AlastairHolmes  
> Created at: 2019-10-29 16:19:37 UTC  
> Updated at: 2020-02-13 11:39:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/631  

I am using boost 1.70.0 and MSVC 2017 15.8 C++17. In the code below the result of the sym_difference is `MULTIPOLYGON(((0 0,100 -2147483648,0 -2147483648,0 0)))` when is should be `MULTIPOLYGON(((0 0,100 0,100 -2147483648,0 -2147483648,0 0)))` Note -2147483648 is INT_MIN.  
  
```  
using mypoint_t = boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>;  
using mypolygon_t = boost::geometry::model::polygon<mypoint_t>;  
{  
	mypolygon_t polygonA;  
	boost::geometry::read_wkt("POLYGON((0 0,0 100,100 100,100 0,0 0))", polygonA);  
	mypolygon_t polygonB;  
	boost::geometry::read_wkt("POLYGON((0 -2147483648,0 100,100 100,100 -2147483648,0 -2147483648))", polygonB);  
	boost::geometry::model::multi_polygon<mypolygon_t> polygonC;  
	boost::geometry::sym_difference(polygonA, polygonB, polygonC);  
}  
```  
But when its changed to use INT_MAX the result is correct:  
```  
{  
	mypolygon_t polygonA;  
	boost::geometry::read_wkt("POLYGON((0 0,0 100,100 100,100 0))", polygonA);  
	mypolygon_t polygonB;  
	boost::geometry::read_wkt("POLYGON((0 0,0 2147483647,100 2147483647,100 0,0 0))", polygonB);  
	boost::geometry::model::multi_polygon<mypolygon_t> polygonC;  
	boost::geometry::sym_difference(polygonA, polygonB, polygonC);  
}  
```

---

## Comment 1

> Username: mloskot  
> Created at: 2019-10-29 17:18:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/631#issuecomment-547534907  

> I am using boost 1.70.0 and MSVC 2017 15.8 C++17.   
  
It would be helpful if you tried your case with Boost.Geometry from the current `develop` branch.

---

## Comment 2

> Username: AlastairHolmes  
> Created at: 2019-10-30 15:19:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/631#issuecomment-547959646  

I get exactly the same results with the development branch. Again with MSVC 2017 15.8 C++17

---

## Comment 3

> Username: mloskot  
> Created at: 2019-10-30 15:21:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/631#issuecomment-547960934  

@AlastairHolmes Thank you for testing.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2019-11-06 10:57:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/631#issuecomment-550258620  

INT_MAX is not just a large value, it is the max value.  
Internally calculations are made (e.g. determinate) multiplying values. We use larger types for this, but apparently this is not enough for the values you use.  
  
I suggest to use another coordinate type for your point (int_64t?).  
  
Assigning to @awulkiew to verify if this statement is correct and/or might be fixed.

---

## Comment 5

> Username: awulkiew  
> Created at: 2020-02-13 11:39:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/631#issuecomment-585704902  

@barendgehrels @AlastairHolmes Well, we use larger types in some places but not all of them, and the library is not tested for this use case.  
  
So your suggestion to use bigger type is good. I'd only add that if the size of geometries is an issue it would be possible to store smaller coordinates as you do and implement thin wrappers around your geometries (something which we call views) returning coordinates of greater type on the fly.
