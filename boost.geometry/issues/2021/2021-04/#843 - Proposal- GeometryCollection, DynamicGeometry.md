# #843 - Proposal: GeometryCollection, DynamicGeometry [Closed]

> Username: awulkiew  
> Created at: 2021-04-21 11:57:39 UTC  
> Updated at: 2021-06-03 14:14:51 UTC  
> Closed at: 2021-06-03 14:14:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/843  

I'd like to propose addition of GeometryCollection, DynamicGeometry and making the support for TupledGeometry public. I made a prototype for these concepts here: https://github.com/awulkiew/test-geometry-collection  
  
**DynamicGeometry** is any type using type-erasure idiom allowing to store various StaticGeometries transparently. Examples:  
- `boost::variant`  
- `boost::variant2::variant2`  
- `std::variant`  
- `boost::any`  
- `std::any`  
- User-defined class internally using abstract class and hierarchy of geometries  
  
The concept:  
- `traits::tag<G>::type` is `dynamic_geomery_tag`  
- `traits::geometry_types<G>::type` is `util::type_sequence` storing all of the StaticGeometries G can hold  
- (mutable) G's assignment operator taking any of the types from `traits::geometry_types<G>::type` is defined  
- `traits::visit<G>::apply` visits geometry  
- (optional) `traits::visit<G1, G2>::apply` visits two geometries, by default 1-argument `visit` is applied 2 times  
  
Alternatives:  
- replace `traits::geometry_types<G>::type` defining `util::type_sequence` with traits returning number of types and allowing to get a type defined by some Index, mimicking `std::variant_size` and `std::variant_alternative`.  
- replace `traits::visit` with `get<>` (then it wouldn't be possible to rely on multiple variants visitation)  
  
**GeometryCollection** is a ForwardRange storing any type that allows the use of DynamicGeometry, even by proxy, so not necessarily storing DynamicGeometry itself. Examples:  
- `std::vector<boost::any>`  
- `bg::model::dynamic_geometry<boost::variant<...>>`  
- UserDefined class holding e.g. a container of DynamicGeometries, a container of pointers to DynamicGeometries, etc.  
  
The concept:  
- `traits::tag<GC>::type` is `geometry_collection_tag`  
- adapted to Boost.Range ForwardRange concept.  
- (mutable) `traits::clear`  
- (mutable) `traits::emplace_back` adding StaticGeometry to the collection (probably could be called `emplace` for even more genericity, e.g. to work well with sets/maps)  
- (optional) `traits::visit_iterator<GC>::apply` visits geometry pointed by the iterator of GC, by default this trait calls `traits::visit<boost::range_value<GC>::type>::apply`, so ranges of DynamicGeometries  
- (optional) `traits::geometry_types<GC>::type` is `util::type_sequence` storing all of the StaticGeometries GC can hold, by default this is `traits::geometry_types<boost::range_value<GC>::type>::type`, so ranges of DynamicGeometries  
  
There is no RandomAccess and no `resize()` since we have to work on StaticGeometries anyway. E.g. it'd be enough to create all needed geometries first and then move them to the GC at the end.   
  
GeometryCollection can be recursive.  
  
What do you think? I'm opened to suggestions.

---

## Comment 1

> Username: awulkiew  
> Created at: 2021-04-21 13:12:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/843#issuecomment-824050108  

On top of that I implemented several utilities to show how the traits are used:  
- `bg::visit(function, g)`  
- `bg::visit(function, g1, g2)`  
- `bg::visit_breadth_first(function, g)`  
  
So e.g. the whole `resolve_variant::area` part:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/area.hpp#L276-L320  
could be reimplemented as:  
```  
typename area_result<Geometry>::type result = 0;  
bg::visit_breadth_first([&](auto const& g)  
{  
    result += resolve_strategy::area<Strategy>::apply(g, strategy);  
}, geometry);  
```  
assuming `area_result` works with DynamicGeometries and GeometryCollection. This would cover all of the kinds of geometries I wrote above.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2021-05-12 08:37:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/843#issuecomment-839583307  

Hi @awulkiew , I think this looks really useful!  
Can you make a PR?
