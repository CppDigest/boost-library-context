# #789 Defined a geometry for polyhedron [Closed]

> Username: Siddharth-coder13  
> Created at: 2020-12-28 10:39:58 UTC  
> Updated at: 2025-05-12 12:25:27 UTC  
> Closed at: 2025-05-12 12:25:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/789  

As suggested by @barendgehrels and @vissarion in comments #683, I defined geometry for polyhedral surfaces.

---

## Review 1 [Changes requested]

> Username: barendgehrels  
> Created_at: 2020-12-28 18:23:42 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/789#pullrequestreview-559252147  

Looks like a good start!  
  
But I can't see how it works yet. It looks quite unfinished.  
  
Can you add   
1) a unit test, specifically for the polyhedron itself  
2) a unit test, for the wkt support (it seems as started too) of the polyhedron, to read it and to write it?  
  
As far as I can judge the code, it won't work yet, so you will probably need to modify parts of the source code too.

📁 include/boost/geometry/geometries/polyhedron.hpp

```diff
  41 |+ 	typedef Point point_type;
  42 |+     typedef ring<Point, ClockWise, Closed, PointList, PointAlloc> ring_type;
  43 |+     typedef RingList<ring_type, RingAlloc<ring_type > > polyhedron_type;
```

> Username: barendgehrels  
> Created_at: 2020-12-28 18:18:44 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r549439234  

You can use `using` now i/o `typedef`

> Username: Siddharth-coder13  
> Created_at: 2020-12-29 05:39:40 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r549575268  

Ok, I will modify the codes.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2020-12-28 18:25:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/789#pullrequestreview-559254555  

📁 include/boost/geometry/geometries/polyhedron.hpp

```diff
  57 |+ 
  58 |+ #endif
  59 |+ 
```

> Username: barendgehrels  
> Created_at: 2020-12-28 18:25:15 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r549444280  

where's the contents of the polyhedron itself?

> Username: Siddharth-coder13  
> Created_at: 2020-12-29 05:44:55 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r549575996  

I implemented two constructors,  one for default and another for an initialized list, which takes ring_type as elements. So, the ring has its own methods, if there is a need to add methods to a polyhedron, I will add them.


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2020-12-28 18:26:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/789#pullrequestreview-559254881  

📁 include/boost/geometry/geometries/polyhedron.hpp

```diff
  27 |+ <
  28 |+     typename Point,
  29 |+     bool ClockWise = true, bool Closed = true,
```

> Username: barendgehrels  
> Created_at: 2020-12-28 18:26:15 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r549444585  

What is the purpose of `Clockwise`?  
What is the purpose of `Closed`?  
  
If you add closed clockwise rings as surfaces, isn't that enough?  
  
What is the purpose of the pointlist?

> Username: Siddharth-coder13  
> Created_at: 2020-12-29 05:47:44 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r549576483  

I found similar type of implementation for polygon, so I implemented that for Polyhedron.   
`Pointlist`, `Closed` and `Clockwise` are the parameter for the ring.

> Username: vissarion  
> Created_at: 2020-12-29 15:40:35 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r549750373  

`ClockWise` doesn't make any sense in 3D, right? You have a set of oriented rings so it makes sense to talk about consistent orientation or not.   
  
`Closed` makes sense since you may have a closed polyhedron (that bounds a solid with finite volume) or an open polyhedron with infinite volume.

> Username: vissarion  
> Created_at: 2020-12-29 15:43:34 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r549751524  

If those parameters are the ring parameters then why you do not use more descriptive names like `RingClockWise` ? Then, you assume that all rings will have the same order (e.g. clockwise), and all of them will be closed etc. which sounds problematic.


---

## Comment 4

> Username: Siddharth-coder13  
> Created_at: 2020-12-29 05:52:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#issuecomment-751956367  

>   As far as I can judge the code, it won't work yet, so you will probably need to modify parts of the source code too.  
  
I checked the code, after using it in one of the examples and tried to make sure that it doesn't throw any error, but I think there is a need to implement wkt support for a polyhedron, to see its working.

---

## Review 5 [Commented]

> Username: vissarion  
> Created_at: 2020-12-29 15:25:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/789#pullrequestreview-559580680  

📁 include/boost/geometry/geometries/polyhedron.hpp

```diff
  39 |+ public :
  40 |+ 
  41 |+ 	typedef Point point_type;
```

> Username: vissarion  
> Created_at: 2020-12-29 15:25:23 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r549744841  

indentation seems wrong here


---

## Review 6 [Changes requested]

> Username: vissarion  
> Created_at: 2020-12-29 15:50:16 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/789#pullrequestreview-559587325  

@Siddharth-coder13 thanks for the efforts!   
  
From the design of the code I understand that the concept of a polyhedron is not very clear to you. I recommend to read the OGC standard e.g. https://www.ogc.org/standards/sfa and construct some unit tests (as @barendgehrels suggested). This will give you more intuition.  
  
I also recommend to change the name from `Polyhedron` to `PolyhedralSurface` as in the standard.

---

## Review 7 [Commented]

> Username: vissarion  
> Created_at: 2020-12-29 15:51:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/789#pullrequestreview-559591915  

📁 include/boost/geometry/geometries/polyhedron.hpp

```diff
  22 |+ namespace model
  23 |+ {
  24 |+ 
```

> Username: vissarion  
> Created_at: 2020-12-29 15:51:08 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r549754266  

Please add doxygen comments here to describe the parameters of the geometry.


---

## Review 8 [Changes requested]

> Username: barendgehrels  
> Created_at: 2020-12-30 09:31:47 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/789#pullrequestreview-559966792  

It should be simpler, clearer and more concrete.

📁 include/boost/geometry/core/tags.hpp

```diff
 107 | 
 108 |+ /// OGC Polyhedral surface identifying tag
 109 |+ struct PolyhedralSurface_tag : single_tag, volumetric_tag {};
```

> Username: barendgehrels  
> Created_at: 2020-12-30 09:14:54 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r550065927  

But it should never contain capitals... I suggest `polyhedral_surface_tag`

> Username: Siddharth-coder13  
> Created_at: 2020-12-30 09:40:59 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r550086378  

Ok, I will fix this.


📁 include/boost/geometry/geometries/PolyhedralSurface.hpp

```diff
  33 |+     template<typename> class RingAlloc = std::allocator
  34 |+ >
  35 |+ class PolyhedralSurface : public PointList<ring<Point>, RingAlloc<ring<Point> > >
```

> Username: barendgehrels  
> Created_at: 2020-12-30 09:19:40 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r550069400  

I'm quite puzzled by this...  
So it inherits from a `vector<ring>` - that sounds reasonable, but why is it called `PointList`? and where is the `RingList` used? But in the type (below) it's defined as a `RingList`.

---

📁 include/boost/geometry/geometries/PolyhedralSurface.hpp

```diff
  41 |+ 	//typedef Point point_type;
  42 |+     using point_type = Point;
  43 |+     using ring_type =  ring<Point, ClockWise, Closed, PointList, PointAlloc>;
```

> Username: barendgehrels  
> Created_at: 2020-12-30 09:22:58 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r550071801  

So you make use of the `ring` model. That's basically fine, but can't you simplify this surface a lot by just specifying the ring type used?  
  
So then it would look like:  
```  
template  
<  
    typename Ring,  
    template<typename, typename> class Container = std::vector,  
    template<typename> class Allocator = std::allocator  
>  
class polyhedral_surface : public Container<Ring, Allocator<Ring> >  
...  
```  
  
And you can use it like:  
```  
using point_type = model::point<double, 3, bg::cs::cartesian>;  
using ring_type = model::ring<Point, true, true>;  
using ph = model::polyhedral_surface<ring_type>;  
...  
```  
That way you don't have to bother about open, closed, clockwise, it's all defined by the ring concept itself, and you've only one list of rings defining your 3d surface.

> Username: Siddharth-coder13  
> Created_at: 2020-12-30 09:44:05 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r550088841  

Ok, I got this. I will have to specify the ring type rather than using a default `ring` model


📁 include/boost/geometry/io/wkt/detail/prefix.hpp

```diff
  50 |+ struct prefix_polyhedron
  51 |+ {
  52 |+ 	static inline const char* apply() { return "POLYHEDRON"; }
```

> Username: barendgehrels  
> Created_at: 2020-12-30 09:30:53 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r550078312  

as far as I can see it will look like  
```  
POLYHEDRALSURFACE Z ( PATCHES  
    ((0 0 0, 0 1 0, 1 1 0, 1 0 0, 0 0 0)),  
    ((0 0 0, 0 1 0, 0 1 1, 0 0 1, 0 0 0)),  
    ((0 0 0, 1 0 0, 1 0 1, 0 0 1, 0 0 0)),  
    ((1 1 1, 1 0 1, 0 0 1, 0 1 1, 1 1 1)),  
    ((1 1 1, 1 0 1, 1 0 0, 1 1 0, 1 1 1)),  
    ((1 1 1, 1 1 0, 0 1 0, 0 1 1, 1 1 1))  
  )  
```  
(see wikipedia), I don't see anything of this reflected in your code.  
  
Before accepting a pull request with completely new elements like this, it should be a complete part, we should be able to understand the intentions, and there should be a working sample, reading a polyhedral surface from such a WKT and writing it back.  
  
And there is more: it should be concept based.  
  
The `REGISTER` functionality can be omitted from the first PR - it is too far ahead to judge how it should look like.

> Username: Siddharth-coder13  
> Created_at: 2020-12-30 09:47:40 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r550091650  

> (see wikipedia), I don't see anything of this reflected in your code.  
  
yeah, I am working on it. Once I fix the PolyhedralSuface header file, I will complete the wkt read and write.

> Username: Siddharth-coder13  
> Created_at: 2020-12-31 08:52:20 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r550432196  

Do, I need to include the term `PATCHES` in wkt read or write, as it is self-understood from the term `POLYHEDRALSURFACE` ?

> Username: mloskot  
> Created_at: 2020-12-31 09:35:38 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r550440776  

Interesting, I'm not sure where does the `PATCHES` come from in the Wikipedia, but it does not look right.  
AFAIS, there is no such WKT tag specified/presented in the OGC SFS 1.2.1  
  
AFAIK, neither PostGIS nor GDAL/OGR implementation reads/writes it.   
  
From personal experience, I've never used it in any WKT implementations I've done myself.

> Username: barendgehrels  
> Created_at: 2020-12-31 13:43:56 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r550486129  

Indeed, I see. It's probably not from the specs indeed. So please skip the `PATCHES` keyword.  
  
So it will then be something like   
```  
POLYHEDRALSURFACE(  
	((0 0 0, 0 0 1, 0 1 1, 0 1 0, 0 0 0)),  
	((0 0 0, 0 1 0, 1 1 0, 1 0 0, 0 0 0)),  
	((0 0 0, 1 0 0, 1 0 1, 0 0 1, 0 0 0)),  
	((1 1 0, 1 1 1, 1 0 1, 1 0 0, 1 1 0)),  
	((0 1 0, 0 1 1, 1 1 1, 1 1 0, 0 1 0)),  
	((0 0 1, 1 0 1, 1 1 1, 0 1 1, 0 0 1)))  
```  
(taken from https://postgis.net/docs/ST_GeomFromEWKT.html)


---

## Comment 9

> Username: Siddharth-coder13  
> Created_at: 2021-01-06 13:56:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#issuecomment-755312619  

After spending a week on the codebase, it seems to me that wkt input and output depends on multiple header files, including dimensions check and concept check.  
  
To read inputs, I added the following codes:-  
1. `read.hpp` -   
```  
    template<typename PolyhedralSurface>  
    struct polyhderal_surface_parser  
    {  
        typedef typename ring_return_type<PolyhedralSurface>::type ring_return_type;  
        typedef container_appender<ring_return_type> appender;  
  
        static inline void apply(tokenizer::iterator& it,  
                                 tokenizer::iterator const& end,  
                                 std::string const& wkt,  
                                 ring_return_type& ring)  
        {  
            handle_open_parenthesis(it, end, wkt);  
            int i = 0;  
            while(it != end && *it != ")"){  
                appender::apply(it, end, wkt, ring);  
                i++;  
            }  
            handle_close_parenthesis(it, end, wkt);  
       
       }  
   };  
```  
2. `ring_type.hpp` -  
```  
    template <typename PolyhedralSurface>  
    struct ring_return_type<polyhedral_surface_tag, PolyhedralSurface>  
    {  
        typedef PolyhedralSurface& type;  
    };  
```  
```  
   template <typename PolyhedralSurface>  
   struct ring_type<polyhedral_surface_tag, PolyhedralSurface>  
   {  
       typedef typename std::remove_reference  
           <  
               typename ring_return_type<polyhedral_surface_tag, PolyhedralSurface>::type  
           >::type type;  
   };  
```  
3. `PolyhedralSurface_concept.hpp`-  
```  
    #ifndef BOOST_GEOMETRY_GEOMETRIES_CONCEPTS_POLYHEDRALSURFACE_HPP  
    #define BOOST_GEOMETRY_GEOMETRIES_CONCEPTS_POLYHEDRALSURFACE_HPP  
  
    #include <boost/concept_check.hpp>  
    #include <boost/range/concepts.hpp>  
    #include <boost/geometry/core/access.hpp>  
    #include <boost/geometry/core/ring_type.hpp>  
    #include <boost/geometry/geometries/concepts/ring_concept.hpp>  
  
    namespace boost { namespace geometry { namespace concepts   
    {  
  
  
    template <typename Geometry>  
    class PolyhedralSurface  
    {  
  
    #ifndef DOXYGEN_NO_CONCEPT_MEMBERS  
  
	    typedef typename ring_type<Geometry>::type ring_type;  
	  
	  
   	    //BOOST_CONCEPT_ASSERT( (concepts::Ring<ring_type>) );  
  
    public:  
	  
	    BOOST_CONCEPT_USAGE(PolyhedralSurface)  
	    {  
  
		    Geometry* polyhedralSurface = 0;  
		    ring_type* ring = 0;  
  
	    }   
    #endif  
	  
    };  
  
    } // namepspace concepts  
  
    } // namespace geometry  
  
    } // namespace boost  
    #endif   
  
```  
I don't know why I am getting this error every time I build   
  
    file included from ../../../boost/geometry/core/ring_type.hpp:27,  
                 from ../../../boost/geometry/core/closure.hpp:24,  
                 from ../../../boost/geometry/geometry.hpp:34,  
                 from 01_point_example.cpp:15:  
    ../../../boost/geometry/core/coordinate_dimension.hpp: In instantiation of ‘struct   
    boost::geometry::traits::dimension<boost::geometry::model::ring<boost::geometry::model::point<double, 3,     
    boost::geometry::cs::cartesian>, true, true>, void>’:  
    ../../../boost/geometry/core/coordinate_dimension.hpp:63:8:   required from ‘struct   
    boost::geometry::core_dispatch::dimension<boost::geometry::point_tag,   
    boost::geometry::model::ring<boost::geometry::model::point<double, 3, boost::geometry::cs::cartesian>, true, true> >’  
    ../../../boost/geometry/core/coordinate_dimension.hpp:58:8:   required from ‘struct   
    boost::geometry::core_dispatch::dimension<boost::geometry::polyhedral_surface_tag,   
    boost::geometry::model::PolyhedralSurface<boost::geometry::model::ring<boost::geometry::model::point<double, 3,   
    boost::geometry::cs::cartesian>, true, true> > >’  
    ../../../boost/geometry/core/coordinate_dimension.hpp:88:8:   required from ‘struct  
     boost::geometry::dimension<boost::geometry::model::PolyhedralSurface<boost::geometry::model::ring<boost::geometry::  
    model::point<double, 3, boost::geometry::cs::cartesian>, true, true> > >’  
    ../../../boost/geometry/io/wkt/read.hpp:567:51:   required from ‘bool boost::geometry::detail::wkt::initialize(const tokenizer&,   
    const string&, const string&, boost::tokenizer<boost::char_separator<char> >::iterator&,   
    boost::tokenizer<boost::char_separator<char> >::iterator&) [with Geometry =   
    boost::geometry::model::PolyhedralSurface<boost::geometry::model::ring<boost::geometry::model::point<double, 3,   
    boost::geometry::cs::cartesian>, true, true> >; boost::geometry::detail::wkt::tokenizer =   
    boost::tokenizer<boost::char_separator<char> >; std::string = std::__cxx11::basic_string<char>;   
    boost::tokenizer<boost::char_separator<char> >::iterator = boost::token_iterator<boost::char_separator<char>,   
    __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, std::__cxx11::basic_string<char> >]’  
    ../../../boost/geometry/io/wkt/read.hpp:596:33:   required from ‘static void   
    boost::geometry::detail::wkt::geometry_parser<Geometry, Parser, PrefixPolicy>::apply(const string&, Geometry&) [with   
    Geometry = boost::geometry::model::PolyhedralSurface<boost::geometry::model::ring<boost::geometry::model::point<double,   
    3, boost::geometry::cs::cartesian>, true, true> >; Parser = boost::geometry::detail::wkt::polyhderal_surface_parser;   
    PrefixPolicy = boost::geometry::detail::wkt::prefix_polyhedral_surface; std::string = std::__cxx11::basic_string<char>]’  
    ../../../boost/geometry/io/wkt/read.hpp:942:70:   required from ‘void boost::geometry::read_wkt(const string&, Geometry&) [with   
    Geometry = boost::geometry::model::PolyhedralSurface<boost::geometry::model::ring<boost::geometry::model::point<double,   
    3, boost::geometry::cs::cartesian>, true, true> >; std::string = std::__cxx11::basic_string<char>]’  
    01_point_example.cpp:124:282:   required from here  
    ../../../boost/geometry/core/static_assert.hpp:28:81: error: static assertion failed: Not implemented for this Point type.  
       28 | static_assert(boost::geometry::detail::static_assert_check<false, __VA_ARGS__>::value, MESSAGE)  
          |                                                                                 ^~~~~  
    ../../../boost/geometry/core/coordinate_dimension.hpp:45:5: note: in expansion of macro   
    ‘BOOST_GEOMETRY_STATIC_ASSERT_FALSE’  
       45 |     BOOST_GEOMETRY_STATIC_ASSERT_FALSE(  
          |     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  
I am also getting more error like the above, I need some help in understanding why this error turns up every time.

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2021-01-07 17:13:12 UTC  
> Updated_at: 2021-01-07 17:13:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#issuecomment-756252709  

The message indicates that the code expects a point, or an instance implementing the Point Concept, but it is not a point.  
  
Glancing through your code, the problem is probably here.  
  
> appender::apply(it, end, wkt, ring);  
  
and it wants to append a point to a ring - but you get a ring, because you've a collection of rings here. Is that possible? It's just a guess, I didn't compile your code.   
You might comment that line and see if the error disappears (of course it won't work correctly then, but at least you know the line which needs some fixing).

---

## Comment 11

> Username: Siddharth-coder13  
> Created_at: 2021-02-07 09:17:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#issuecomment-774640509  

Hey, @barendgehrels will you please review this PR, as I have implemented wkt read feature for polyhedral surfaces and I will also implement wkt write feature in a new PR, once this one gets merged.

---

## Comment 12

> Username: Siddharth-coder13  
> Created_at: 2021-02-09 05:42:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#issuecomment-775682381  

@mloskot @vissarion @awulkiew

---

## Comment 13

> Username: mloskot  
> Created_at: 2021-02-11 22:42:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#issuecomment-777841388  

@Siddharth-coder13 Somehow this PR #789 and PR #782 have become a mess and they both contain the same commits (e.g. `Defined a geometry for polyhedron` that is `0961662e6e45878eda378f85dd734ae1922f7110`). I have to admit this makes it quite impossible to review. I don't know what happened.

---

## Comment 14

> Username: Siddharth-coder13  
> Created_at: 2021-02-12 01:16:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#issuecomment-777903158  

> @Siddharth-coder13 Somehow this PR #789 and PR #782 have become a mess and they both contain the same commits (e.g. `Defined a geometry for polyhedron` that is `0961662e6e45878eda378f85dd734ae1922f7110`). I have to admit this makes it quite impossible to review. I don't know what happened.  
  
Actually, this PR is the most current version of my local repo, it contains all the changes I have done so far, since my last PR doesn't gets merged, so I have to make a new branch in order to contribute.  
There are three main commits :-  
1. Prefix wkt:z for 3d geometries.  
2. Definition of polyhedral surfaces.  
3. Wkt read implementation.

---

## Comment 15

> Username: mloskot  
> Created_at: 2021-02-12 07:48:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#issuecomment-778036674  

It's unfortunate. The 1 needs to be in the other separate PR and 2+3 as a separate feature in this PR.  
  
I understand this may make your Git operations less convenient but these two are distinct features, they should be treated independently.

---

## Comment 16

> Username: Siddharth-coder13  
> Created_at: 2021-02-12 08:04:59 UTC  
> Updated_at: 2021-02-12 08:05:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#issuecomment-778043471  

> It's unfortunate. The 1 needs to be in the other separate PR and 2+3 as a separate feature in this PR.  
>   
> I understand this may make your Git operations less convenient but these two are distinct features, they should be treated independently.  
  
Yeah, I know both the things are different, using git sometimes is quite complex.  I guess the only thing I can do now is to revert 1, and then this PR will become a separate PR for polyhedral surfaces.  
  
You may review 2 and 3 if you have any changes to suggest, as I want this PR to get merged, for 1 I will submit another PR after discussion if Z is to add or not.

---

## Comment 17

> Username: mloskot  
> Created_at: 2021-02-12 08:33:42 UTC  
> Updated_at: 2021-02-12 09:46:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#issuecomment-778055679  

I'd strongly suggest to not to close any existing PRs and to not to open any new PRs.  
  
> 1. Prefix wkt:z for 3d geometries.  
  
This should be in the PR #782   
  
> 2. Definition of polyhedral surfaces.  
> 3. Wkt read implementation.  
  
These two should be in this PR #789  
  
Since there should be no dependency between the two PRs, that is, the changes in #782 are not required by the #789, then I'd simply suggest to:  
  
1. In each of your local branches from which you submitted these PRS remove all commits that do not belong.  
      - You can `git rebase -i`, or  
      - You can rename this broken `3d-geometries` local branch to e.g. `old/3d-geometries`, create new branch named `3d-geometries` based on `develop`, then `git cherry`-pick` correct commits from `old/3d-geometries` to `3d-geometries`  
2. Finally, `git push --force` to update the PRs.  
  
By the way, this situation should also give you why it is a bad idea to develop any contributions in your fork's `develop` branch (like you did in PR  #782). Instead, always create a topic branch and submit PR from a topic branch. That way, you can easily 'replace' PR branches in your remote without affecting your local `develop` branch. Topic branches save time!

---

## Comment 18

> Username: vissarion  
> Created_at: 2021-02-12 09:29:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#issuecomment-778082441  

@Siddharth-coder13 as an addition to the excellent comments from @mloskot you could also have a look at https://github.com/boostorg/geometry/wiki/Contribution-Tutorial

---

## Review 19 [Changes requested]

> Username: mloskot  
> Created_at: 2021-02-23 09:52:55 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/789#pullrequestreview-596155471  

A few nit-picks

📁 example/01_point_example.cpp

```diff
 117 |     {{1, 1, 1}, {1, 1, 0}, {0, 1, 0}, {0, 1, 1}, {1, 1, 1}} };
 118 |+     
 119 |+     std::cout<<wkt(polyhedron2)<<std::endl;
```

> Username: mloskot  
> Created_at: 2021-02-23 09:50:36 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r580897076  

You are missing spaces around `<<`


📁 include/boost/geometry/geometries/concepts/PolyhedralSurface_concept.hpp

```diff
  40 |+ 
  41 |+ 	typedef typename std::remove_const<Geometry>::type const_polyhedral_type;
  42 |+ 
```

> Username: mloskot  
> Created_at: 2021-02-23 09:50:54 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r580897308  

Unnecessary blank line

---

📁 include/boost/geometry/geometries/concepts/PolyhedralSurface_concept.hpp

```diff
  46 |+ 
  47 |+ public:
  48 |+ 
```

> Username: mloskot  
> Created_at: 2021-02-23 09:51:00 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r580897375  

Unnecessary blank line

---

📁 include/boost/geometry/geometries/concepts/PolyhedralSurface_concept.hpp

```diff
  49 |+ 	BOOST_CONCEPT_USAGE(ConstPolyhedral)
  50 |+ 	{
  51 |+ 
```

> Username: mloskot  
> Created_at: 2021-02-23 09:51:06 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r580897440  

Unnecessary blank line

---

📁 include/boost/geometry/geometries/concepts/PolyhedralSurface_concept.hpp

```diff
  54 |+ #endif
  55 |+ };
  56 |+ 
```

> Username: mloskot  
> Created_at: 2021-02-23 09:51:22 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r580897618  

Unnecessary blank line

---

📁 include/boost/geometry/geometries/concepts/PolyhedralSurface_concept.hpp

```diff
  39 |+ #ifndef DOXYGEN_NO_CONCEPT_MEMBERS
  40 |+ 
  41 |+ 	typedef typename std::remove_const<Geometry>::type const_polyhedral_type;
```

> Username: mloskot  
> Created_at: 2021-02-23 09:52:32 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r580898535  

It's C++14 now, you can use `using` to declare aliases.


📁 include/boost/geometry/geometries/concepts/check.hpp

```diff
 129 |+ template <typename Geometry>
 130 |+ struct check<Geometry, polyhedral_surface_tag, true>
 131 |+     : detail::concept_check::check<concepts::ConstPolyhedral<Geometry> >
```

> Username: mloskot  
> Created_at: 2021-02-23 09:52:01 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r580898135  

Since the source code requires C++14, the extra space between `>` and `>` is no longer required and should not be cultivated in new code

> Username: Siddharth-coder13  
> Created_at: 2021-02-23 11:42:01 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r580967951  

will remove the extra space...


---

## Comment 20

> Username: Siddharth-coder13  
> Created_at: 2021-02-27 11:03:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#issuecomment-787055138  

Hey @mloskot, I think this PR is able to merge now.

---

## Comment 21

> Username: Siddharth-coder13  
> Created_at: 2021-02-27 11:05:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#issuecomment-787055344  

> Hey @mloskot, I think this PR is able to merge now.  
  
or suggest changes if any.

---

## Comment 22

> Username: barendgehrels  
> Created_at: 2021-02-27 13:55:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#issuecomment-787076545  

> > Hey @mloskot, I think this PR is able to merge now.  
>   
> or suggest changes if any.  
  
It’s big, important and needs to be reviewed and approved properly, by multiple people.

---

## Comment 23

> Username: mloskot  
> Created_at: 2021-02-27 18:43:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#issuecomment-787117451  

@Siddharth-coder13 Please, hit the "Resolve conversation" for the items that you have addressed. This way, we can see what's been done and what's not.

---

## Review 24 [Changes requested]

> Username: barendgehrels  
> Created_at: 2021-03-03 10:26:08 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/789#pullrequestreview-602715560  

Thanks for the update!  
  
It's much better, but not ready yet. See my comments.  
  
As asked earlier:  
  
Can you add  
  
1. a unit test, specifically for the polyhedron itself (without wkt)  
      a: create it from scratch  
      b: modify it  
      c: iterate through it  
      d: clear it  
      e: check the concepts  
    such that we can really see how it works internally?  
  
2. a unit test, for the wkt support of the polyhedral surface, to read it and to write it?  
3. documentation including an example for the user (`doc/src/examples/geometries/polyhedral_surface.cpp`)  
  
And please check yourself for consistencies in capitilization of filenames and struct/class names, for the style (I know in the current code we've many places where `C++11` is not yet applied - but now we can)

📁 example/01_point_example.cpp

```diff
 110 |+     typedef model::point<double, 3, cs::cartesian> point_t;
 111 |+     typedef model::ring<point_t> ring_t;
 112 |+     typedef model::PolyhedralSurface<ring_t> poly_t;
```

> Username: barendgehrels  
> Created_at: 2021-03-03 10:01:53 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r586278972  

This does not belong in `01_point_example.cpp`. Can you please create a completely new example?  
I would prefer `08_polyhedralsurface_example.cpp` with this code?

---

📁 example/01_point_example.cpp

```diff
 123 |+     //append(polyhedron1[0], point_t{0, 1, 0});
 124 |+     //append(polyhedron1[0], point_t{1, 1, 0});
 125 |+     //append(polyhedron1[0], point_t{0, 0, 0});
```

> Username: barendgehrels  
> Created_at: 2021-03-03 10:02:17 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r586279250  

Please avoid commented code like this in a PR to be merged.

---

📁 example/01_point_example.cpp

```diff
 127 |+ 
 128 |+     std::cout << wkt(polyhedron1) << std::endl;
 129 |+ 
```

> Username: barendgehrels  
> Created_at: 2021-03-03 10:03:46 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r586280287  

The added code below looks like a small personal test.  
At any rate, it doesn't belong to point / polyhedron and can just be deleted, I think.


📁 include/boost/geometry/algorithms/clear.hpp

```diff
 128 |+ struct clear<Geometry, polyhedral_surface_tag>
 129 |+     : detail::clear::no_action<Geometry>
 130 |+ {};
```

> Username: barendgehrels  
> Created_at: 2021-03-03 10:04:54 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r586281065  

Why there is no action? Why the polyhedral surface is not cleared?


📁 include/boost/geometry/core/point_type.hpp

```diff
  93 | };
  94 | 
  95 |+ // Specialization for PolyhedralSurface: the point-type is the point-type of its rings
```

> Username: barendgehrels  
> Created_at: 2021-03-03 10:06:52 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r586282549  

`Of its rings` -> please change to `of its ...` (whatever is our name for the surfaces making up the polyhedron)

> Username: Siddharth-coder13  
> Created_at: 2021-03-24 15:15:05 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r600579761  

poly_ring_type is a good name I guess

---

📁 include/boost/geometry/core/point_type.hpp

```diff
 101 |+             ring_tag,
 102 |+             typename ring_type<polyhedral_surface_tag, PolyhedralSurface>::type
 103 |+         >::type type;
```

> Username: barendgehrels  
> Created_at: 2021-03-03 10:07:03 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r586282686  

You can use `using` now


📁 include/boost/geometry/geometries/PolyhedralSurface.hpp

```diff
  31 |+ 
  32 |+ >
  33 |+ class PolyhedralSurface : public Container<Ring, Allocator<Ring> >
```

> Username: barendgehrels  
> Created_at: 2021-03-03 10:09:25 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r586284475  

We never use capitals in classes or structures. So it would be `polyhedral_surface`

---

📁 include/boost/geometry/geometries/PolyhedralSurface.hpp

```diff
  51 |+ 	inline PolyhedralSurface(std::initializer_list<ring_type> l)
  52 |+ 	    : ph(l.begin(), l.end())
  53 |+ 	{}
```

> Username: barendgehrels  
> Created_at: 2021-03-03 10:09:54 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r586284817  

Indentation is wrong

> Username: Siddharth-coder13  
> Created_at: 2021-03-14 11:08:02 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r593882632  

I don't know why it's showing the wrong indentation here, my sublime code doesn't show it.

> Username: mloskot  
> Created_at: 2021-03-14 16:02:41 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r593922300  

You may be mixing spaces and tabs. You need to ensure you're using spaces only, no tabs.

---

📁 include/boost/geometry/geometries/PolyhedralSurface.hpp

```diff
  29 |+     template<typename, typename> class Container = std::vector,
  30 |+     template<typename> class Allocator = std::allocator
  31 |+ 
```

> Username: barendgehrels  
> Created_at: 2021-03-03 10:10:06 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r586284987  

empty line

---

📁 include/boost/geometry/geometries/PolyhedralSurface.hpp

```diff
  87 |+     template<typename> class Allocator
  88 |+ >
  89 |+ struct Poly_ring_type
```

> Username: barendgehrels  
> Created_at: 2021-03-03 10:11:06 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r586285681  

`poly_ring_type` is a better name.

---

📁 include/boost/geometry/geometries/PolyhedralSurface.hpp

```diff
  41 |+     using ph = Container<ring_type, Allocator<ring_type> >;
  42 |+ 
  43 |+ #ifndef BOOST_NO_CXX11_HDR_INITIALIZER_LIST
```

> Username: barendgehrels  
> Created_at: 2021-03-03 10:11:42 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r586286120  

@awulkiew  do we still need these defines in new code?  
I don't think so.


📁 include/boost/geometry/geometries/concepts/PolyhedralSurface_concept.hpp

```diff
  12 |+ 
  13 |+ template <typename Geometry>
  14 |+ class PolyhedralSurface
```

> Username: barendgehrels  
> Created_at: 2021-03-03 10:12:20 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r586286583  

For Concepts, indeed, we use capitals in the names so this is fine.

---

📁 include/boost/geometry/geometries/concepts/PolyhedralSurface_concept.hpp

```diff
  28 |+ //polyhedral surface(constant version)
  29 |+ template <typename Geometry>
  30 |+ class ConstPolyhedral
```

> Username: barendgehrels  
> Created_at: 2021-03-03 10:12:41 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r586286825  

`Surface` is missing


📁 include/boost/geometry/geometries/concepts/check.hpp

```diff
  41 | #include <boost/geometry/geometries/concepts/segment_concept.hpp>
  42 |- 
  42 |+ #include <boost/geometry/geometries/concepts/PolyhedralSurface_concept.hpp>
```

> Username: barendgehrels  
> Created_at: 2021-03-03 10:13:38 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r586287460  

The capitilization of the filename is is wrong


📁 include/boost/geometry/geometries/geometries.hpp

```diff
  30 | #include <boost/geometry/geometries/ring.hpp>
  31 | #include <boost/geometry/geometries/segment.hpp>
  32 |+ #include <boost/geometry/geometries/PolyhedralSurface.hpp>
```

> Username: barendgehrels  
> Created_at: 2021-03-03 10:14:18 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r586287932  

Here too wrong capitilization of the filename


📁 include/boost/geometry/geometries/register/PolyhedralSurface.hpp

```diff
   9 |+ namespace boost { namespace geometry { namespace traits {  \
  10 |+     template<> struct tag<PolyhedralSurface> { typedef PolyhedralSurface_tag type; }; \
  11 |+ }}}
```

> Username: barendgehrels  
> Created_at: 2021-03-03 10:15:21 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r586288652  

As I mentioned earlier, we can omit this (this whole file) in this PR. It's too early.  
I'm curious if users will ever need it.

> Username: Siddharth-coder13  
> Created_at: 2021-03-14 11:13:21 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r593883191  

Ok, should I delete the file or comment out the codes?


📁 include/boost/geometry/io/wkt/read.hpp

```diff
 466 |+     {
 467 |+         handle_open_parenthesis(it, end, wkt);
 468 |+         while(it != end && *it != ")"){
```

> Username: barendgehrels  
> Created_at: 2021-03-03 10:15:46 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r586289007  

Please put the curly brace at next line

---

📁 include/boost/geometry/io/wkt/read.hpp

```diff
 480 |+                 >::apply(Poly, ring);
 481 |+             handle_close_parenthesis(it, end, wkt);
 482 |+             if(it!=end && *it == ",")
```

> Username: barendgehrels  
> Created_at: 2021-03-03 10:16:00 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r586289160  

Please take care for the spacing

---

📁 include/boost/geometry/io/wkt/read.hpp

```diff
 561 |     end = tokens.end();
 562 | 
 563 |+     //std::cout<<*it<<" "<<geometry_name<<std::endl;
```

> Username: barendgehrels  
> Created_at: 2021-03-03 10:16:14 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r586289291  

You can remove this


📁 include/boost/geometry/io/wkt/write.hpp

```diff
 249 |+ 
 250 |+         os << "(";
 251 |+         for( typename boost::range_iterator<Polyhedral_surface const>::type
```

> Username: barendgehrels  
> Created_at: 2021-03-03 10:16:35 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r586289498  

You can use `auto` now

---

📁 include/boost/geometry/io/wkt/write.hpp

```diff
 244 |+     {
 245 |+         typedef typename std::remove_const<Polyhedral_surface>::type const_polyhedral_type;
 246 |+         typedef typename ring_type<const_polyhedral_type>::type ring;
```

> Username: barendgehrels  
> Created_at: 2021-03-03 10:16:47 UTC  
> Updated_at: 2021-04-03 04:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#discussion_r586289631  

you can use `using` now (everywhere in this PR)


---

## Comment 25

> Username: Siddharth-coder13  
> Created_at: 2021-03-05 04:19:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#issuecomment-791141203  

Hey, @barendgehrels Thanks for your reviews, but I think this point needs a separate PR, because a single PR can not define everything about polyhedral surfaces.  
> 3. documentation including an example for the user (`doc/src/examples/geometries/polyhedral_surface.cpp`)  
  
I will do the rest changes asap.

---

## Comment 26

> Username: barendgehrels  
> Created_at: 2021-03-11 18:12:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#issuecomment-796938508  

>   
>   
> Hey, @barendgehrels Thanks for your reviews, but I think this point needs a separate PR, because a single PR can not define everything about polyhedral surfaces.  
>   
> > 1. documentation including an example for the user (`doc/src/examples/geometries/polyhedral_surface.cpp`)  
>   
> I will do the rest changes asap.  
  
Thank you! Success!  
I think a separate PR for documentation will be fine.

---

## Comment 27

> Username: Siddharth-coder13  
> Created_at: 2021-04-12 05:26:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#issuecomment-817491597  

Hello @mloskot @barendgehrels @vissarion @awulkiew, as I have extended this PR as a project in GSOC'21 including other features of polyhedral surfaces. I want to get this PR merged before the GSOC period starts. It would be of great help if you leave your comment on this.

---

## Comment 28

> Username: barendgehrels  
> Created_at: 2021-05-12 08:32:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#issuecomment-839579607  

Hi @Siddharth-coder13 , sorry for the late reply. I think it looks good now!  
  
There is no unit test yet, did you plan to do that in this PR or in a next one (along with the documentation)? Documentation we can do later but a unit test is normally included in a PR (especially for new stuff).

---

## Comment 29

> Username: Siddharth-coder13  
> Created_at: 2021-05-12 19:21:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#issuecomment-840036975  

Hi @barendgehrels , thanks for the review. Actually, I added the example file of polyhedral surfaces, if unit test is needed in this PR, then I will add it asap.

---

## Comment 30

> Username: vissarion  
> Created_at: 2025-05-12 12:25:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/789#issuecomment-2872354097  

Implemented by this PR https://github.com/boostorg/geometry/pull/1402

---
