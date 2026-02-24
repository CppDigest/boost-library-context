# #89 - Use-after-free on 64-bit coordinates [Open]

> Username: plopresti  
> Created at: 2023-12-11 21:47:26 UTC  
> Updated at: 2023-12-11 21:47:26 UTC  
> Url: https://github.com/boostorg/polygon/issues/89  

```  
#include <boost/polygon/polygon.hpp>  
#include <iterator>  
#include <cstdint>  
  
namespace bp = boost::polygon;  
  
typedef int64_t Coord;  
typedef bp::point_data<Coord> Vec2;  
typedef bp::polygon_data<Coord> Polygon;  
typedef bp::polygon_set_data<Coord> PolySet;  
  
int main()  
{  
    const Vec2 pts[] = {  
        Vec2(0,0),  
        Vec2(-219215130788,1374389535),  
        Vec2(-195163313930,204784040673), // BAD  
        //Vec2(-195163313929,204784040673), // OK  
    };  
  
    Polygon poly;  
    set_points(poly, std::cbegin(pts), std::cend(pts));  
    PolySet pset;  
    pset.insert(poly);  
    std::vector<Polygon> c;  
    pset.get_trapezoids(c);  
}  
```  
  
Compile with -fsantitize=address to see the use-after-free error, although my glibc complains even without it.  
  
Note that these coordinates are all less than 40 bits.  
  
The suggestions in #36 (use gmp_override.hpp) and #39 (update 64-bit coordinate_traits) do not help.  
  
What does help is changing the last line of evalAtXforYlazy() to `return roundl(evalAtXforYret)`. I do not understand the code well enough to say whether this is a good idea in general.
