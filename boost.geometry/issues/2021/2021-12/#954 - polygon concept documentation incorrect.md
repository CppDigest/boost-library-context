# #954 - polygon concept documentation incorrect [Closed]

> Username: mboedigh  
> Created at: 2021-12-20 00:12:49 UTC  
> Updated at: 2022-12-01 09:06:33 UTC  
> Closed at: 2022-12-01 09:06:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/954  

documentation for [1.78](https://www.boost.org/doc/libs/1_78_0/libs/geometry/doc/html/geometry/reference/concepts/concept_polygon.html) says that template<> traits::ring_type should be specified, but there is no traits::ring_type template. VS2022 shows the error  
> error C2039: 'ring_type': is not a member of 'boost::geometry::traits'  
Should the docs say to specialize  boost::geometry::ring_type<>?  
  
I also needed to specialize several other templates not mentioned in the docs. I don't know if this is a complete list, or if there is a simpler way, but this all seems necessary for me to use boost::geometry::intersection(m3::Polygon &, m3::Polygon&, output_container);  
```  
template <> struct boost::geometry::traits::point_type <m3::Polygon> {     // this was optional for intersection, but needed for other function templates  
using type = m3::Polygon::point_type;   
};  
template <>  
struct boost::geometry::traits::ring_const_type<m3::Polygon> {  
    using type = m3::Polygon::ring_type const &;  
};  
  
template <>  
struct boost::geometry::traits::ring_mutable_type<m3::Polygon> {  
    using type = m3::Polygon::ring_type &;  
};  
template <>  
struct boost::geometry::traits::interior_const_type<m3::Polygon> {  
    using type = m3::Polygon::inner_container_type const &;  
};  
  
template <>  
struct boost::geometry::traits::interior_mutable_type<m3::Polygon> {  
    using type = m3::Polygon::inner_container_type &;  
};  
```
