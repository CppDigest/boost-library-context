# #63 - Crash with VS 16.8.6 but working with clang 11/13 and gcc 11 [Open]

> Username: nicolasLA  
> Created at: 2021-03-29 16:23:44 UTC  
> Updated at: 2022-04-05 09:17:25 UTC  
> Url: https://github.com/boostorg/polygon/issues/63  

Hi there,  
  
We encountered a weird error running the following code using boost::polygon.  
  
It is compiling and working on:  
- wandbox.org using clang/gcc  
- and on macOS using Apple clang 11.0.3.  
  
and compiling but crashing with cl from Visual Studio 16.8.6.  
  
Do you have any idea about what is going on please?  
  
Cheers,  
Nicolas  
  
```  
#include <vector>  
#include <boost/polygon/polygon.hpp>  
  
int main()  
{  
	using Polygon = boost::polygon::polygon_data<int>;  
	using PolygonPoints = std::vector<typename Polygon::point_type>;  
	PolygonPoints polygonPoints{  
        { 0, 0 },  
        { -37570000, 37568665 },  
        { -233490372, -195921616 },  
    };  
	Polygon const polygon{ std::begin(polygonPoints), std::end(polygonPoints) };  
      
	boost::polygon::polygon_set_data<int> polygonSet;  
	polygonSet.insert(polygon);  
  
	std::vector<Polygon> trapezoids;  
	polygonSet.get_trapezoids(trapezoids);  
      
    std::cout << "#trapezoids: " << trapezoids.size() << std::endl;  
      
    auto index{ 0 };  
      
    for (auto const& trapezoid: trapezoids)  
    {  
        std::cout << "trapezoid " << index << ":" << std::endl;  
        auto pointIndex{ 0 };  
          
        for (auto const& coord: trapezoid.coords_)  
        {  
            std::cout << "point " << pointIndex << ": " << coord.x() << "; " << coord.y() << std::endl;  
            ++pointIndex;  
        }  
          
        ++index;  
    }  
}  
```

---

## Comment 1

> Username: nicolasLA  
> Created at: 2021-09-16 14:41:19 UTC  
> Updated at: 2021-09-16 14:41:47 UTC  
> Url: https://github.com/boostorg/polygon/issues/63#issuecomment-920964351  

Bump.  
Still the case with VS 16.11.3

---

## Comment 2

> Username: nicolasLA  
> Created at: 2022-04-05 09:17:25 UTC  
> Url: https://github.com/boostorg/polygon/issues/63#issuecomment-1088467005  

Bump.  
Still the case with VS 16.11.11
