# #695 Support tupled-output in difference, sym_difference and union_. [Merged]

> Username: awulkiew  
> Created at: 2020-04-13 14:28:48 UTC  
> Updated at: 2020-05-13 15:04:05 UTC  
> Merged at: 2020-05-13 15:04:04 UTC  
> Closed at: 2020-05-13 15:04:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/695  

This is a followup PR after https://github.com/boostorg/geometry/pull/650  
  
It adds support for tupled-output in the rest of set operations. The interface is the same for all of them and allows the user to use the same output type. Here is an example:  
```  
polygon po1, po2;  
bg::read_wkt("POLYGON((0 0, 0 5, 5 5, 5 0, 0 0))", po1);  
bg::read_wkt("POLYGON((0 0, 0 1, 6 1, 5 2, 5 5, 5 6, 4 5, 4 7, 7 7, 7 0, 0 0))", po2);  
bg::correct(po1);  
bg::correct(po2);  
  
std::tuple<multi_point, multi_linestring, multi_polygon> d, i, sd, u;  
bg::difference(po1, po2, d);  
bg::intersection(po1, po2, i);  
bg::sym_difference(po1, po2, sd);  
bg::union_(po1, po2, u);  
  
auto print_result = [&](char op, auto const& res) {  
    std::cout << bg::wkt(po1) << ' ' << op << ' ' << bg::wkt(po2) << '\n'  
                << bg::wkt(std::get<0>(res)) << '\n'  
                << bg::wkt(std::get<1>(res)) << '\n'  
                << bg::wkt(std::get<2>(res)) << '\n'  
                << std::endl;  
};  
  
print_result('-', d);  
print_result('*', i);  
print_result('^', sd);  
print_result('+', u);  
```  
![image](https://user-images.githubusercontent.com/1226951/79127903-f06bec00-7da2-11ea-9669-92c6064fe3a0.png)  
  
The output is:  
```  
POLYGON((0 0,0 5,5 5,5 0,0 0)) - POLYGON((0 0,0 1,6 1,5 2,5 5,5 6,4 5,4 7,7 7,7 0,0 0))  
MULTIPOINT()  
MULTILINESTRING()  
MULTIPOLYGON(((0 1,0 5,5 5,5 1,0 1)))  
```  
![image](https://user-images.githubusercontent.com/1226951/79128046-2c9f4c80-7da3-11ea-9e6a-ed617ca38d2c.png)  
  
```  
POLYGON((0 0,0 5,5 5,5 0,0 0)) * POLYGON((0 0,0 1,6 1,5 2,5 5,5 6,4 5,4 7,7 7,7 0,0 0))  
MULTIPOINT((4 5))  
MULTILINESTRING((5 5,5 2))  
MULTIPOLYGON(((0 1,5 1,5 0,0 0,0 1)))  
```  
![image](https://user-images.githubusercontent.com/1226951/79128203-6e2ff780-7da3-11ea-8d6c-6ff7c07d63c2.png)  
  
```  
POLYGON((0 0,0 5,5 5,5 0,0 0)) ^ POLYGON((0 0,0 1,6 1,5 2,5 5,5 6,4 5,4 7,7 7,7 0,0 0))  
MULTIPOINT()  
MULTILINESTRING()  
MULTIPOLYGON(((4 5,4 7,7 7,7 0,5 0,5 1,0 1,0 5,4 5),(5 5,5 6,4 5,5 5),(5 2,5 1,6 1,5 2)))  
```  
![image](https://user-images.githubusercontent.com/1226951/79128256-7f790400-7da3-11ea-9cb9-b87db9579189.png)  
  
```  
POLYGON((0 0,0 5,5 5,5 0,0 0)) + POLYGON((0 0,0 1,6 1,5 2,5 5,5 6,4 5,4 7,7 7,7 0,0 0))  
MULTIPOINT()  
MULTILINESTRING()  
MULTIPOLYGON(((0 5,4 5,4 7,7 7,7 0,0 0,0 5),(5 1,6 1,5 2,5 1),(5 5,5 6,4 5,5 5)))  
```  
![image](https://user-images.githubusercontent.com/1226951/79128278-86a01200-7da3-11ea-87ea-33afc9c4a3f7.png)  
  
Note that in this case the set operations other than intersection can produce only multi polygon but this is not the case when the input has different topological dimention. E.g. when a linestring and a polygon is passed into union the result will be a polygon and the difference of the linestring and the polygon. So the use of tupled output will have more sense.

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2020-04-22 08:38:28 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/695#pullrequestreview-397957369  

Thanks for the PR! It is large and quite complex so I didn't get into all of the details. In general it looks good to me and I'm OK with merging it.  
  
I've made a few small remarks.

📁 include/boost/geometry/algorithms/detail/tupled_output.hpp

```diff
 668 |+ 
 669 |+ template <typename CastedTag>
 670 |+ struct casted_tag_to_single_tag;
```

> Username: barendgehrels  
> Created_at: 2020-04-22 08:25:37 UTC  
> Updated_at: 2020-05-05 18:47:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/695#discussion_r412777597  

Names of casts are typically suffixed with `cast`. Can you maybe rename this to `to_single_tag_cast` or `single_tag_cast` ?

> Username: awulkiew  
> Created_at: 2020-05-04 04:14:25 UTC  
> Updated_at: 2020-05-05 18:47:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/695#discussion_r419206938  

This is not a cast. It's a metafunction translating/mapping from `CastedTag` (dispatch template argument which can be pointlike_tag, linear_tag, areal_tag) to tag of a "single" geometry which is an element of multi-geometry (point_tag, linestring_tag, polygon_tag respectively).

> Username: awulkiew  
> Created_at: 2020-05-05 18:48:51 UTC  
> Updated_at: 2020-05-05 18:48:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/695#discussion_r420331462  

I renamed it to `single_tag_from_base_tag` to avoid "cast" word. Is that name better?


📁 include/boost/geometry/algorithms/union.hpp

```diff
 349 |     concepts::check<Geometry2 const>();
 212 |-     concepts::check<GeometryOut>();
 350 |+     //concepts::check<GeometryOut>();
```

> Username: barendgehrels  
> Created_at: 2020-04-22 08:32:40 UTC  
> Updated_at: 2020-05-05 18:47:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/695#discussion_r412782880  

This line is replaced and I think you can delete it?

> Username: awulkiew  
> Created_at: 2020-05-05 18:47:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/695#discussion_r420330826  

Done.

---

📁 include/boost/geometry/algorithms/union.hpp

```diff
 115 |     typename Linear1, typename Linear2, typename LineStringOut,
 142 |-     bool Reverse1, bool Reverse2, bool ReverseOut
 116 |+     typename TagIn1, typename TagIn2
```

> Username: barendgehrels  
> Created_at: 2020-04-22 08:33:46 UTC  
> Updated_at: 2020-05-05 18:47:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/695#discussion_r412783716  

Where are the Reverse parameters now? (I can probably look that up but in a review screen that is not so clear)

---

📁 include/boost/geometry/algorithms/union.hpp

```diff
 226 |+                 overlay_difference,
 227 |+                 geometry::detail::overlay::do_reverse<geometry::point_order<Geometry1>::value>::value,
 228 |+                 geometry::detail::overlay::do_reverse<geometry::point_order<Geometry2>::value, true>::value
```

> Username: barendgehrels  
> Created_at: 2020-04-22 08:34:49 UTC  
> Updated_at: 2020-05-05 18:47:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/695#discussion_r412784495  

oh, probably this is the place where `Reverse` is done now


---
