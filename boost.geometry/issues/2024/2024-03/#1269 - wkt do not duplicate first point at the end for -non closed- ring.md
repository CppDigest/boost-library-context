# #1269 - wkt do not duplicate first point at the end for "non closed" ring [Closed]

> Username: GS6902  
> Created at: 2024-03-29 19:50:36 UTC  
> Updated at: 2024-06-27 08:37:59 UTC  
> Closed at: 2024-06-27 08:37:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/1269  

Hello  
  
The following code give "POLYGON((0 0,0 1,1 1,1 0))"  
I expected "POLYGON((0 0,0 1,1 1,1 0, 0 0))"  
  
the last point was not repeated.  
  
A ring is supposed to be always closed.   
I used a "non closed" ring in my code. But I have understood that template parameter as a convenience way to omit internally the last redundant point. But I suppose the result of the function should not change.  
  
I suppose we should use the closing iterator in this algorithm.  
  
Am I wrong ?  
  
```  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/geometries.hpp>  
  
namespace bg  = boost::geometry;  
namespace mdl = boost::geometry::model;  
namespace cs  = boost::geometry::cs;  
  
int main()  
   {  
   constexpr bool IsClosed = false;  
   using CPoint = mdl::point<double, 2, cs::cartesian>;  
   using CRing = mdl::ring<CPoint, true, IsClosed>;  
   CRing Ring {  
      { 0, 0 },  
      { 0, 1 },  
      { 1, 1 },  
      { 1, 0 }  
   };  
   const bool IsValid = bg::is_valid(Ring);  
   assert(IsValid);  
  
   std::ostringstream Oss;  
   Oss << bg::wkt(Ring);  
   std::string String = Oss.str();  
   std::cout << String;  
   return 0;  
   }  
```

---

## Comment 1

> Username: vissarion  
> Created at: 2024-04-05 08:27:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/1269#issuecomment-2039237180  

If you define a ring as non closed and then you omit the repeated point it is a valid geometry. If you define a ring as closed and then you omit the repeated point the geometry is non valid. You can run `correct` algorithm and then the geometry will be corrected to have the repeated point. So I think everything is as expected here.  
  
Interestingly though, if you define a geometry to be open (as in your case) and you include the repeated point in the creation then the geometry is valid. @barendgehrels  is this intended?

---

## Comment 2

> Username: GS6902  
> Created at: 2024-04-05 17:43:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/1269#issuecomment-2040323402  

> If you define a ring as non closed and then you omit the repeated point it is a valid geometry. If you define a ring as closed and then you omit the repeated point the geometry is non valid. You can run `correct` algorithm and then the geometry will be corrected to have the repeated point. So I think everything is as expected here.  
>   
> Interestingly though, if you define a geometry to be open (as in your case) and you include the repeated point in the creation then the geometry is valid. @barendgehrels is this intended?  
  
Thanks a lot for your answer,   
My point is that a non closed ring is an oxymoron.   
If I follow you a non closed ring (a ring with template parameter set as non closed) is just non closed and a synonym of a linestring ?  
Or it is a ring. So it is closed but by convinience the last point can be omitted in case of a "non closed" ring (by setting the template parameter)  
  
In that case I suppose the result of valid ring (closed and non closed) shouldn't change because it model the same concept

---

## Comment 3

> Username: vissarion  
> Created at: 2024-04-08 13:42:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/1269#issuecomment-2042795830  

The correct case is the second case you mentioned. A non closed ring is not a linestring.
