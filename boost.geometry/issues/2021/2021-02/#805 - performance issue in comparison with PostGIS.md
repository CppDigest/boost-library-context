# #805 - performance issue in comparison with PostGIS [Closed]

> Username: nikitamikhaylov  
> Created at: 2021-02-12 14:31:09 UTC  
> Updated at: 2021-03-19 19:18:33 UTC  
> Closed at: 2021-03-19 19:18:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/805  

Hello, I just tried to implement a bunch of geometry function in ClickHouse. https://github.com/ClickHouse/ClickHouse/pull/19257  
  
These functions are pure wrappers around boost functions except some code to convert our types into boost types.   
  
I benchmarked them in comparison to PostGIS on dataset of all countries and was very confused. Boost.Geometry code is about **20 times slower** on functions like intersection or union. But functions to calculate area or perimeter archieve pretty the same performance in both databases.   
  
https://github.com/ClickHouse/ClickHouse/pull/19257#issuecomment-777597878  
  
`perf top` shows that the most of the time we spend in boost  https://github.com/ClickHouse/ClickHouse/pull/19257#issuecomment-778184448  
  
Is it normal? Maybe I use boost.geometry in a wrong way? What algorithms do you use to intersect polygons on the spheroid?

---

## Comment 1

> Username: awulkiew  
> Created at: 2021-02-12 16:11:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/805#issuecomment-778287518  

Hi Nikita,  
  
The main part of the algorithms like intersection or union in Boost.Geometry is the same for all coordinate systems. In all cases we speed things up with a form of kd-tree. What is different is e.g. how the bounding boxes of segments are calculated or how the intersection of two segments is calculated internally. E.g. for intersection we use [Sjoberg's formulas](https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/formulas/sjoberg_intersection.hpp) on spheroid by default calculating inverse geodesic problem (e.g. to get azimuths) using [Andoyer's formula](https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/formulas/andoyer_inverse.hpp) and approximating the Sjoberg's integrals with a series of order 1 in eccentricity squared. See the links to articles in the hpp files.  
  
You can change the default behavior by passing the geographic strategy into an algorithm but if you pass only the geometries using geographic coordinate system this is what is used. The default one is also the fastest and least accurate approximation we use for geographic CS.  
  
Regarding PostGIS I don't know what method do they use so I don't know whether or not this is something that should be expected. AFAIK they do not use Sjoberg's formulas so this may be one of the differences.  
  
Are the results of both PostGIS and BG the same (esspecially for long segments)?  
  
Do you know how are the segments in PostGIS represented? Do they use linear interpolation between geographic points in the space of geographic coordinates, are they represented as great circles on a sphere or spheroid or as geodesics on spheroid?  
  
Have you tried to use `spherical_equatorial` coordinate system with BG?

---

## Comment 2

> Username: nikitamikhaylov  
> Created at: 2021-02-12 17:12:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/805#issuecomment-778322972  

> Have you tried to use `spherical_equatorial` coordinate system with BG?  
  
Yes, but there were no big changes in performance.  
  
> Are the results of both PostGIS and BG the same (esspecially for long segments)?  
  
It seems that for union function it is true, but for intersection there were some difference.   
  
For other questions the answer is `I don't know`, because I know nothing in this area. I asked for the algorithm only because I see a way to implement it directly in our codebase.

---

## Comment 3

> Username: nikitamikhaylov  
> Created at: 2021-02-25 19:29:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/805#issuecomment-786146547  

@awulkiew Hello! Is there some kind of dispatching depending on the hardware in runtime inside boost::geometry? I've faced a problem, that boost::geometry::area and boost::geometry::polygon return different results on different machines, but all of there results are deterministic. So, on my machine it constantly returns one result, on other machine - other. Please, help.

---

## Comment 4

> Username: awulkiew  
> Created at: 2021-03-19 19:15:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/805#issuecomment-803054394  

@nikitamikhaylov This is a different issue than the original one. Please use the mailing list for questions in the future. Or https://gitter.im/boostorg/geometry .   
  
> Is there some kind of dispatching depending on the hardware in runtime inside boost::geometry?  
  
No, there is not. You may experience different numerical issues depending not only on hardware but also compiler. In general you won't get exactly the same results and the more complex the calculation the more difference you may get. There may also be a bug so if you think some results are wrong on some machines or with some compilers please create an issue.

---

## Comment 5

> Username: awulkiew  
> Created at: 2021-03-19 19:18:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/805#issuecomment-803055551  

@nikitamikhaylov Btw, if you don't see any difference between spherical and geographic computation then my guess would be that the bootleneck is somewhere else.
