# #782 Fixed Writing 3D WKT:Z prefix [Open]

> Username: Siddharth-coder13  
> Created at: 2020-12-09 05:32:16 UTC  
> Updated at: 2025-05-16 09:30:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/782  

Changed the `wkt_point` struct to take 3 parameters rather than 2, for one for 2d and one for 3d, added the `prefix_z` function in `prefix.hpp,` and also added a line of code to print 3d points in `01_point_example.cpp` .

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2020-12-09 08:35:23 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/782#pullrequestreview-547946969  

Thanks for the contribution.  
  
Since every new improvement or feature needs to be covered with test case(s), you may consider adding some to https://github.com/boostorg/geometry/tree/develop/test/io/wkt

---

## Comment 2

> Username: mloskot  
> Created_at: 2020-12-11 15:56:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#issuecomment-743275433  

@awulkiew In https://github.com/boostorg/geometry/issues/664#issuecomment-583736980 you suggested  
> we'd need a separate function for that.  
  
What did you have in mind?  
  
This PR seems to follow my suggestion I made in https://github.com/boostorg/geometry/issues/664#issuecomment-583773745  
  
@Siddharth-coder13 The commit and description of PR that addresses an existing issue should contain reference to the issue (i.e. `#664`)

---

## Comment 3

> Username: Siddharth-coder13  
> Created_at: 2020-12-11 16:02:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#issuecomment-743278900  

Yeah, sure from next time I will make sure this.

---

## Comment 4

> Username: Siddharth-coder13  
> Created_at: 2020-12-13 09:30:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#issuecomment-743978871  

Do this PR needs more changes? @mloskot

---

## Comment 5

> Username: mloskot  
> Created_at: 2020-12-13 09:34:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#issuecomment-743979264  

@Siddharth-coder13 No, for now it does not, but we need to wait and clarify my question https://github.com/boostorg/geometry/pull/782#issuecomment-743275433 in order to confirm this is actually the kind of change we need and want.

---

## Comment 6

> Username: Siddharth-coder13  
> Created_at: 2020-12-29 06:10:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#issuecomment-751960226  

@awulkiew,  your reviews are needed on this PR.

---

## Review 7 [Changes requested]

> Username: barendgehrels  
> Created_at: 2020-12-30 09:50:06 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/782#pullrequestreview-559978085  

Thanks for the PR but it's not yet clear enough if we want to have a `Z` by default.

📁 include/boost/geometry/io/wkt/write.hpp

```diff
 122 |-         os << Policy::apply() << "(";
 122 |+         if(dimension<Point>::type::value == 3) os << Policy2::apply() << "(";
 123 |+         else os << Policy1::apply() << "(";
```

> Username: barendgehrels  
> Created_at: 2020-12-30 09:47:57 UTC  
> Updated_at: 2021-02-20 07:12:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#discussion_r550091847  

Question: do we always want a `Z` in our 3D point? Until now we just did without and it works without.  
If we suddenly add a `Z` here it might be incompatible for users (if any) who use this functionality. So it's a breaking change.  
  
Apart from this it should be a compile time condition i/o a runtime condition. So we should dispatch based on dimension. But I'm not sure about that either, because if it is optional (as it probably should be), that might be inconvenient.

> Username: barendgehrels  
> Created_at: 2020-12-30 09:55:20 UTC  
> Updated_at: 2021-02-20 07:12:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#discussion_r550097589  

As far as I can see the specification indeed mentions a `Z` for three dimensional points. It's unclear if a three dimensional point without a `Z` is acceptable or not.  
But what do we do with four dimensional points? Or even more? We've not much support for them of course, but we can calculate distances for them...  
  
In my personal opinion, for a three dimensional point, a list with three coordinates is already enough. In reading we can read or not read the `Z` (that is already implemented).

> Username: vissarion  
> Created_at: 2021-01-11 15:16:46 UTC  
> Updated_at: 2021-02-20 07:12:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#discussion_r555118507  

I would agree with @barendgehrels   
  
Another issue here is that after this change the following code   
```  
boost::geometry::read_wkt("POINT M(1 2 3)", p);  
std::cout << boost::geometry::wkt(p) << std::endl;  
```  
will output  
      
```POINT Z(1 2 3)```  
  
I think we should decide how we want to support more than 2 dimensions and probably issue https://github.com/boostorg/geometry/issues/664 is the right place for that.

> Username: mloskot  
> Created_at: 2021-01-11 16:50:10 UTC  
> Updated_at: 2021-02-20 07:12:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#discussion_r555191611  

@barendgehrels points out a valid issue.  
  
> But what do we do with four dimensional points?  
  
From the OGC SFS point, we have:  
- OGC SFS 1.1.0 restricted to 2D geometries   
- The extended WKT/ or 2.5D WKT implemented by OGR, PostGIS, etc. allow 2, 3, 4 dimension coordinates and do not use `Z` or `M` tags, see [Adam Gawne-Cain, 1999, Simple Features Revision Proposal](https://gis.stackexchange.com/a/79189/939)  
- OGC SFS 1.2.x allows 2, 3 and 4 dimensions using the `Z` or `M` tags:  `POINT Z(x, y, z)`,   `POINT M(x, y, m)`,   `POINT ZM(x, y, z, m)`. I think many may consider `M` as pseudo-dimension or not a dimension but a measure value.  
  
>  a list with three coordinates is already enough  
  
It seems desirable indeed to keep the current WKT I/O that does not read/output the `Z` and `M` tags.  
  
Perhaps, an acceptable solution would be an optional `flavor` parameter, or similar, with current  
  
- `boost::geometry::read_wkt(wkt, geometry, flavor`  
- `boost::geometry::wkt(geometry, flavor)`  
  
where `flavor` is enum, something like:  
  
```cpp  
enum /*class*/ wkt_flavour  
{  
  ogc2d,        // OGC SFS 1.1.0  
  ogc3d,        // OGC SFS 1.2.x  
  ogc25d,       // Adam Gawne-Caine, 1999, Simple Features Revision Proposal  
  ewkt = ogc25d // PostGIS terminology  
};  
```  
  
or as an entirely separate OGC-compliant `boost/geometry/gis/io/wkt/` extension.


---

## Review 8 [Changes requested]

> Username: vissarion  
> Created_at: 2021-01-11 15:17:01 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/782#pullrequestreview-565420289  

📁 include/boost/geometry/io/wkt/write.hpp

```diff
 121 |     {
 122 |-         os << Policy::apply() << "(";
 122 |+         if(dimension<Point>::type::value == 3) os << Policy2::apply() << "(";
```

> Username: vissarion  
> Created_at: 2021-01-11 14:46:31 UTC  
> Updated_at: 2021-02-20 07:12:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#discussion_r555096699  

A note about style here, there should be an empty space between `if` and `(`, there are not one line `if`s etc. You may want to consult https://github.com/boostorg/geometry/wiki/Guidelines-for-Developers


---

## Review 9 [Commented]

> Username: mloskot  
> Created_at: 2021-02-11 22:21:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/782#pullrequestreview-589037081  

📁 example/01_point_example.cpp

```diff
 110 |+     typedef model::point<double, 3, cs::cartesian> point_t;
 111 |+     typedef model::ring<point_t> ring_t;
 112 |+     typedef model::PolyhedralSurface<ring_t> poly_t;
```

> Username: mloskot  
> Created_at: 2021-02-11 22:21:51 UTC  
> Updated_at: 2021-02-20 07:12:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#discussion_r574867497  

Since the library is now C++14, the aliases could be declared with the `using` declarations, which is a bit neater, IMHO, as it puts the most important part, the name, to the left.


---

## Review 10 [Commented]

> Username: mloskot  
> Created_at: 2021-02-11 22:23:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/782#pullrequestreview-589037874  

📁 example/01_point_example.cpp

```diff
 115 |+     poly_t polyhedron2 = {{{0,0,0}, {0, 1, 0}, {1, 1, 0}, {1, 0, 0}, {0, 0, 0}}, {{0, 0, 0}, {0, 1, 0}, {0, 1, 1}, {0, 0, 1}, {0, 0, 0}},
 116 |+     {{0, 0, 0}, {1, 0, 0}, {1, 0, 1}, {0, 0, 1}, {0, 0, 0}}, {{1, 1, 1}, {1, 0, 1}, {0, 0, 1}, {0, 1, 1}, {1, 1, 1}}, {{1, 1, 1}, {1, 0, 1}, {1, 0, 0}, {1, 1, 0}, {1, 1, 1}}, 
 117 |+     {{1, 1, 1}, {1, 1, 0}, {0, 1, 0}, {0, 1, 1}, {1, 1, 1}} };
```

> Username: mloskot  
> Created_at: 2021-02-11 22:23:05 UTC  
> Updated_at: 2021-02-20 07:12:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#discussion_r574868073  

Could you reformat it to something easier to distinguish rows and columns? e.g.  
  
```  
{  
  {{...}},  
  {{...}},  
  {{...}}  
};  
```


---

## Review 11 [Commented]

> Username: mloskot  
> Created_at: 2021-02-11 22:23:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/782#pullrequestreview-589038353  

📁 include/boost/geometry/core/point_type.hpp

```diff
 101 |+             ring_tag,
 102 |+             typename ring_type<polyhedral_surface_tag, PolyhedralSurface>::type
 103 |+         >::type type;
```

> Username: mloskot  
> Created_at: 2021-02-11 22:23:52 UTC  
> Updated_at: 2021-02-20 07:12:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#discussion_r574868459  

The `using` too?


---

## Review 12 [Approved]

> Username: mloskot  
> Created_at: 2021-02-11 22:33:38 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/782#pullrequestreview-589039480  

📁 include/boost/geometry/geometries/PolyhedralSurface.hpp

```diff
  15 |+ #ifndef BOOST_NO_CXX11_HDR_INITIALIZER_LIST
  16 |+ #include <initializer_list>
  17 |+ #endif
```

> Username: mloskot  
> Created_at: 2021-02-11 22:25:41 UTC  
> Updated_at: 2021-02-20 07:12:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#discussion_r574869330  

I'd move this in  these between  
  
```  
#include <vector>  
<---  
#include <boost/concept/assert.hpp>  
```

---

📁 include/boost/geometry/geometries/PolyhedralSurface.hpp

```diff
  39 |+     using point_type = model::point<double, 3, boost::geometry::cs::cartesian>;
  40 |+     using ring_type = ring<point_type, true, true>;
  41 |+     using ph = Container<ring_type, Allocator<ring_type> >;
```

> Username: mloskot  
> Created_at: 2021-02-11 22:26:45 UTC  
> Updated_at: 2021-02-20 07:12:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#discussion_r574869838  

What does `ph` stand for?  
I'd suggest to use full, descriptive names, avoid abbreviations, unless others agree those are fine.


📁 include/boost/geometry/geometries/concepts/PolyhedralSurface_concept.hpp

```diff
  20 |+ 	typedef typename ring_type<Geometry>::type ring_type;
  21 |+ 	
  22 |+ 
```

> Username: mloskot  
> Created_at: 2021-02-11 22:27:23 UTC  
> Updated_at: 2021-02-20 07:12:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#discussion_r574870150  

Too many blank lines

---

📁 include/boost/geometry/geometries/concepts/PolyhedralSurface_concept.hpp

```diff
  27 |+ 	BOOST_CONCEPT_USAGE(PolyhedralSurface)
  28 |+ 	{
  29 |+ 
```

> Username: mloskot  
> Created_at: 2021-02-11 22:27:39 UTC  
> Updated_at: 2021-02-20 07:12:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#discussion_r574870293  

Superfluous blank

---

📁 include/boost/geometry/geometries/concepts/PolyhedralSurface_concept.hpp

```diff
  37 |+ } // namespace geometry
  38 |+ 
  39 |+ } // namespace boost
```

> Username: mloskot  
> Created_at: 2021-02-11 22:28:04 UTC  
> Updated_at: 2021-02-20 07:12:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#discussion_r574870533  

```  
}}} // boost::geometry::concepts  
```


📁 include/boost/geometry/geometries/concepts/check.hpp

```diff
  41 | #include <boost/geometry/geometries/concepts/segment_concept.hpp>
  42 |- 
  42 |+ #include <boost/geometry/geometries/concepts/PolyhedralSurface_concept.hpp>
```

> Username: mloskot  
> Created_at: 2021-02-11 22:28:44 UTC  
> Updated_at: 2021-02-20 07:12:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#discussion_r574870871  

Please, do not use CamelCase in file names (i.e. `PolyhedralSurface_concept.hpp` should read `polyhedralsurface_concept.hpp`


📁 include/boost/geometry/geometries/geometries.hpp

```diff
  30 | #include <boost/geometry/geometries/ring.hpp>
  31 | #include <boost/geometry/geometries/segment.hpp>
  32 |+ #include <boost/geometry/geometries/PolyhedralSurface.hpp>
```

> Username: mloskot  
> Created_at: 2021-02-11 22:29:20 UTC  
> Updated_at: 2021-02-20 07:12:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#discussion_r574871227  

CamelCase


📁 include/boost/geometry/geometries/register/PolyhedralSurface.hpp

```diff
  14 |+ #define BOOST_GEOMETRY_REGISTER_POLYHEDRALSURFACE_TEMPLATED(PolyhedralSurface) \
  15 |+ namespace boost { namespace geometry { namespace traits {  \
  16 |+     template<typename P> struct tag< PolyhedralSurface<P> > { typedef PolyhedralSurface_tag type; }; \
```

> Username: mloskot  
> Created_at: 2021-02-11 22:29:52 UTC  
> Updated_at: 2021-02-20 07:12:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#discussion_r574871511  

```  
< PolyhedralSurface<P> >  
```  
to  
```  
<PolyhedralSurface<P>>  
```


📁 include/boost/geometry/io/wkt/read.hpp

```diff
 467 |+         handle_open_parenthesis(it, end, wkt);
 468 |+         typename ring_type<PolyhedralSurface>::type ring;
 469 |+         while(it != end && *it != ")"){
```

> Username: mloskot  
> Created_at: 2021-02-11 22:30:33 UTC  
> Updated_at: 2021-02-20 07:12:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#discussion_r574871832  

`{` in separate line


---

## Review 13 [Changes requested]

> Username: mloskot  
> Created_at: 2021-02-11 22:35:11 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/782#pullrequestreview-589045033  

📁 include/boost/geometry/io/wkt/detail/prefix.hpp

```diff
  50 |+ struct prefix_polyhedral_surface
  51 |+ {
  52 |+ 	static inline const char* apply() { return "POLYHEDRALSURFACE Z"; }
```

> Username: mloskot  
> Created_at: 2021-02-11 22:35:07 UTC  
> Updated_at: 2021-02-20 07:12:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#discussion_r574873776  

Not sure about the `Z` as per @barendgehrels  https://github.com/boostorg/geometry/pull/782#discussion_r550097589


---

## Comment 14

> Username: mloskot  
> Created_at: 2021-02-11 22:47:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#issuecomment-777843817  

@Siddharth-coder13 Please, see https://github.com/boostorg/geometry/pull/789#issuecomment-777841388

---

## Comment 15

> Username: vissarion  
> Created_at: 2025-05-16 09:30:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/782#issuecomment-2886181797  

@Siddharth-coder13 are you planing to work on this? Otherwise we should close it, it is inactive for 4 years.

---
