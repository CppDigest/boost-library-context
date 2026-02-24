# #517 - Compilation error for the example of adapted legacy geometry object model [Open]

> Username: xventura81  
> Created at: 2018-10-17 21:38:02 UTC  
> Updated at: 2022-11-22 15:08:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/517  

Trying the example code   
[Example model](https://www.boost.org/doc/libs/1_68_0/libs/geometry/doc/html/geometry/examples/example__adapting_a_legacy_geometry_object_model.html)  
[Adapted example model](https://www.boost.org/doc/libs/1_68_0/libs/geometry/doc/html/geometry/examples/example_source_code__adapting_a_legacy_geometry_object_model.html)  
  
... I got the following build error (Visual Studio 15.8.6):  
```  
namespace bg = boost::geometry;  
  
int main()  
{  
	QPolygon polygon;  
	std::vector<QPoint> points = { {0,0}, {0,1}, {1,0} };  
	bg::append(polygon, points);  
}  
```  
  
Error	C2664	'void boost::geometry::detail::append::append_range<QRing,Range>::apply(Geometry &,const Range &,int,int)': cannot convert argument 1 from 'const QRing' to 'Geometry &'	BoostGeometryExample	...\boost.1.68.0.0\lib\native\include\boost\geometry\algorithms\append.hpp	127	  
  
This error is pointing to the following code in append.hpp:  
  
```  
template <typename Polygon, typename Range>  
struct range_to_polygon  
{  
    typedef typename ring_type<Polygon>::type ring_type;  
  
    static inline void apply(Polygon& polygon, Range const& range,  
                int ring_index, int = 0)  
    {  
        if (ring_index == -1)  
        {  
            append_range<ring_type, Range>::apply(  
                        exterior_ring(polygon), range);  
        }  
        else if (ring_index < int(num_interior_rings(polygon)))  
        {  
            append_range<ring_type, Range>::apply(  
                        range::at(interior_rings(polygon), ring_index), range);  
        }  
    }  
};  
```  
  
The r-value returned by `interior_rings` might be being linked to a const& and assume that this might be causing the error.

---

## Comment 1

> Username: xventura81  
> Created at: 2018-10-19 07:29:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/517#issuecomment-431271285  

I think those issues should be early detected through the unit tests. Might we be missing some additional tests using adapted geometry models?
