# #1171 - hausdorff is not symmetric - that should be added to the documentation [Open]

> Username: barendgehrels  
> Created at: 2023-07-03 18:01:24 UTC  
> Updated at: 2023-07-14 18:04:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/1171  

The Hausdorff algorithm is not symmetrical and can give unexpected results.  
  
SEE COMMENTS BELOW - we should fix the documentation.  
  
Minimal case:  
```  
#include <boost/geometry.hpp>  
  
namespace bg = boost::geometry;  
  
template <typename Geometry>  
double fixed_hausdorff(const Geometry& geometry1, const Geometry& geometry2)  
{  
    return std::max(bg::discrete_hausdorff_distance(geometry1, geometry2),  
        bg::discrete_hausdorff_distance(geometry2, geometry1));  
}  
  
template <typename Geometry>  
void test_case(Geometry const& p, Geometry const& q)  
{  
    std::cout   
        << "Frechet: " << boost::geometry::discrete_frechet_distance(p, q)  
        << " Hausdorff: " << boost::geometry::discrete_hausdorff_distance(p, q)  
        << " Hausdorff (q/p): " << boost::geometry::discrete_hausdorff_distance(q, p)  
        << " Fixed Hausdorff: " << fixed_hausdorff(p, q)  
        << std::endl;  
}  
  
int main()  
{  
    using coordinate_type = double;  
    using point = boost::geometry::model::d2::point_xy<coordinate_type>;  
    using linestring = boost::geometry::model::linestring<point>;  
  
    // Two lines consistently 0.1 apart  
    std::string const simplex1 = "LINESTRING(1.0 1.0, 2.0 1.0)";  
    std::string const simplex2 = "LINESTRING(1.0 1.1, 2.0 1.1)";  
  
    // With extra point in between  
    std::string const simplex3 = "LINESTRING(1.0 1.1, 1.5 1.1,2.0 1.1)";  
      
    // With a "spike"  
    std::string const simplex4 = "LINESTRING(1.0 1.1, 1.49 1.1, 1.5 2.5, 1.51 1.1, 2.0 1.1)";  
  
    test_case(bg::from_wkt<linestring>(simplex1), bg::from_wkt<linestring>(simplex2));  
    test_case(bg::from_wkt<linestring>(simplex1), bg::from_wkt<linestring>(simplex3));  
    test_case(bg::from_wkt<linestring>(simplex1), bg::from_wkt<linestring>(simplex4));  
  
   return 0;  
}  
```  
  
It reports:  
```  
Frechet: 0.1 Hausdorff: 0.1 Hausdorff (q/p): 0.1 Fixed Hausdorff: 0.1  
Frechet: 0.509902 Hausdorff: 0.1 Hausdorff (q/p): 0.509902 Fixed Hausdorff: 0.509902  
Frechet: 1.58114 Hausdorff: 0.1 Hausdorff (q/p): 1.58114 Fixed Hausdorff: 1.58114  
```  
  
and you can see it is wrong. I verified it with another (non open source) version of Hausdorff and that gives the same result as here labeled as `Fixed` (also for other testcases).  
  
The `fixed_hausdorff` presented here is probably not optimized. I only tested for linestrings.  
  
The correct version is equal to Frechet, for these testcases (for others, obviously, it's not).

---

## Comment 1

> Username: vissarion  
> Created at: 2023-07-14 14:00:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/1171#issuecomment-1635908338  

Indeed, there are places in the bibliography where Hausdorff distance is defined oriented or assymetric   
$h(A,B) = max_{a\in A}(min_{b\in B}(d(a,b))$ where $d(a,b)$ is the distance between $a$ and $b$. But maybe the most common is the symmetric one i.e. $H(A,B)=max( h(A,B),h(B,A ))$ (the one you call "fixed").   
  
Thus, it is not exactly wrong, it is a matter of definition. However, it is problematic the definition is not given in the documentation.   
  
I am OK to implement the symmetric Hausdorff and also describe it in the documentation. I am also OK if we keep the assymetric explicitly say this in the documentation and let the user decide whether to use the symmetric one by writing a function as your `fixed_hausdorff()`.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2023-07-14 18:03:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/1171#issuecomment-1636203396  

hi @vissarion ,  
  
All right, it makes sense so we don't need to change the implementation. But we should add it to the documentation, so I can leave this ticket open.  
  
I will rename `fixed_hausdorff` later in my own PR to, for example, `symmetric_hausdorff`.  
  
Thanks.
