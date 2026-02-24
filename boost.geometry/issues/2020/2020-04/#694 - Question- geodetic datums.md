# #694 - Question: geodetic datums [Closed]

> Username: JaapAap  
> Created at: 2020-04-05 17:58:15 UTC  
> Updated at: 2020-04-09 14:30:14 UTC  
> Closed at: 2020-04-09 14:30:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/694  

While reading up on the capabilities of the library (I am not a user at this moment), and in particular the design rationale, I noticed that there is a notion of a geographic coordinate system. However, such a system is typically associated with a geodetic datum (WGS84, ED50, NAD83 and many others). Hence, I would have expected support for several geographic coordinate systems - each with their own datum, which would allow the library to work with these transparently.    
  
When searching through the source codes on 'wgs84', I see that geodetic datums do appear throughout the code. For example, coordinate transformations between different datums do exist within the library (see for example the functions defined in pj_transform.hpp), but as far as I see these do not exploit the basic 'point concept' for the different coordinate systems (btw, I don't think there is much documentation on these?).  
  
Is my understanding correct, and is this something that has been looked at already maybe? Or maybe I am just missing something here?

---

## Comment 1

> Username: awulkiew  
> Created at: 2020-04-05 19:06:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/694#issuecomment-609466108  

In Boost.Geometry the geographic CS implies that mathematical operations are performed on the surface of a spheroid and that the segments of geometries (besides boxes) are defined by geodesics. The data models are not bound to any specific datum or spheroid. Parameters of the spheroid can be defined by passing them into a coordinate-system-specific strategy with the default being wgs84 spheroid.  
  
In case of spherical equatorial the operations are performed on the surface of a sphere where segments are defined by great circles and unit sphere is the default.  
  
In cartesian the operations are performed e.g. on a plane.  
  
So I guess this is more general-purpose-oriented. You have to know what operations and in which CS you want to perform. Then convert your data in a specific datum to this CS, e.g. convert between spheroid and sphere or do map projection, etc. And then perform the mathematical operation that you want passing corresponding parameters like spheroid or radius into a strategy.  
  
Example:  
  
```  
bg::model::point<double, 2, bg::cs::geographic<bg::degree>> p1(0, 0), p2(1, 1);  
  
// default wgs84  
double d1 = bg::distance(p1, p2);  
  
bg::srs::spheroid<double> wgs84;  
// explicitly passed wgs84 spheroid  
double d2 = bg::distance(p1, p2, bg::strategy::distance::geographic<>(wgs84));  
  
// different spheroid  
bg::srs::spheroid<double> clarke1866(6378206.4, 6356583.8);  
double d3 = bg::distance(p1, p2, bg::strategy::distance::geographic<>(clarke_1866));  
  
bg::model::point<double, 2, bg::cs::spherical_equatorial<bg::degree>> p3(0, 0), p4(1, 1);  
  
// unit sphere used by default, so multiply by R  
double d4 = bg::distance(p3, p4) * 6370997.0;  
  
// or pass R explicitly  
double d5 = bg::distance(p3, p4, bg::strategy::distance::haversine<>(6370997.0));  
  
// actually the strategy defines the CS of an operation  
// spherical CS is used here even though geographic points are passed  
double d6 = bg::distance(p1, p2, bg::strategy::distance::haversine<>(6370997.0));  
```  
  
The unfortunate part of all of this is that the strategies are not documented well.

---

## Comment 2

> Username: JaapAap  
> Created at: 2020-04-05 19:32:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/694#issuecomment-609469923  

Thanks for that,  I much appreciate your answer and examples!! As you said, my purpose/use case is indeed somewhat more general than what is currently supported. I guess that as a result of my (geodetic) use case, from a conceptual standpoint I would have expected the coordinates to be defined with respect to a datum - it seems unnatural to me (and potentially somewhat error prone) that the computational strategy for distance (or some other quantity) eventually determines in which reference system the coordinates are specified. Would `d6` in your example represent a meaningful quantity at all?

---

## Comment 3

> Username: awulkiew  
> Created at: 2020-04-05 20:43:29 UTC  
> Updated at: 2020-04-05 20:43:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/694#issuecomment-609479556  

> As you said, my purpose/use case is indeed somewhat more general  
  
Actually I had the opposite in mind. AFAIU your use case is domain-specific, GIS-oriented. I meant that Boost.Geometry algorithms has more low-level, general-purpose, STL-inspired interface. E.g. if you were writing a vector graphics editor, or a game, or PCB layout processing app (though this would rather be Boost.Polygon's domain) you would not want to deal with datums at all.  
  
Boost.Geometry's interface is heavily influenced by the interface of the STL and Boost.Range. The difference is that instead of function objects (predicates) specifying some critical parts of the algorithm, in Boost.Geometry strategies are used to define CS-critical parts.  
  
Consider e.g. `std::sort()`, `boost::sort()` or `std::ranges::sort()` overloads. One taking a range (ether as 2 iterators or as a single argument) and the other one in addition to the range taking also a Compare predicate defining how the values in the range are going to be less-compared. If the latter overload is instantiated it doesn't matter whether or not the `operator<` is defined for the range value type because the user explicitly passed a function object defining how the comparison will be performed during sorting. It's the same with Boost.Geometry algorithms.  
  
> Would d6 in your example represent a meaningful quantity at all?  
  
Yes, I understand the confusion if you're coming from the GIS domain.  
  
Think about it like that:  
```  
std::vector<int> v{3, 4, 1, 5, 7};  
std::sort(v.begin(), v.end());  
// vs  
std::sort(v.begin(), v.end(), std::greater<>());  
```  
or:  
```  
int a = std::accumulate(v.begin(), v.end(), 0);  
// vs  
int b = std::accumulate(v.begin(), v.end(), 1, std::multiplies<>());  
```

---

## Comment 4

> Username: JaapAap  
> Created at: 2020-04-09 08:34:17 UTC  
> Updated at: 2020-04-09 09:00:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/694#issuecomment-611405134  

Understood - I guess one man's special case is another man's generalized one - where I considered the 'no datum' case as a special case of a datum, you could indeed also consider the 'datum' case an application-specific specialization that extends the library. Your 'less-compared' analogy makes sense - and I think it made me understand the design better. However, I am still scratching my head on the meaning of  `d6` - your examples suggests to me that the coordinates are 'reinterpreted' as being spherical while in fact they are spheroidal. If that interpretation is correct, I guess this could typically be seen as an approximation (e.g. when both CS represent the earth), but the same would not be true when using cartesian coordinates. Is there be a mechanism that would prevent using those here?

---

## Comment 5

> Username: awulkiew  
> Created at: 2020-04-09 13:23:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/694#issuecomment-611525348  

> your examples suggests to me that the coordinates are 'reinterpreted' as being spherical while in fact they are spheroidal.  
  
Yes, you could treat it as an approximation. For me it's a matter of saying to the library what mathematical formulas should be used by the algorithm.  
  
Or consider this. You could write an app calculating e.g. a distance on the surface of a generalized planet/moon, let's say distances on the Earth and on the Moon so you would express Earth coordinates in WGS84 (EPSG:4326) and Moon coordinates on a sphere (IAU2000:30100). And you could store both kinds of coordinates using the same point type. Technically these are both geographic coordinates right?  
  
> but the same would not be true when using cartesian coordinates  
  
The calculated distance would be the distance in the "space of the geographic coordinates" (in angles) as if the surface of spheroid or sphere was unfolded into the plane naively.  
  
It would have to be a use case where both long and very short distances has to be calculated, the data is in geographical format and the user doesn't want to perform a projection but wants to use cartesian approximation anyway, maybe because of speed. Maybe it would be useful for some processing of OpenStreetMap where the coordinates are geographic ones and one would maybe like to calculate long distances between cities or countries but at the same time perform some operations on small objects like buildings. But I'm guessing here. I don't know how common would something like this be.  
  
> Is there be a mechanism that would prevent using those here?  
  
No, you can pass whatever strategy you like. You could also theoretically implement a strategy calculating some different kind of distance which has nothing to do with the shortest path between points, a metric of some sort, etc. It works like STL algorithms by design.  
  
If you're concerned by it you could always build a level of abstraction between your code and Boost.Geometry only allowing the operations you think are correct. With Boost.Geometry it is also possible to write your own point type and adapt it to the Boost.Geometry [Point concept](https://www.boost.org/doc/libs/1_54_0/libs/geometry/doc/html/geometry/reference/concepts/concept_point.html).

---

## Comment 6

> Username: JaapAap  
> Created at: 2020-04-09 13:44:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/694#issuecomment-611536028  

I might indeed opt for an additional abstraction level and/or a custom point type. I will close the comment as I am happy with my current level of understanding and don't want to claim any more of your time! Many thanks for all your help and providing me with more insight.

---

## Comment 7

> Username: awulkiew  
> Created at: 2020-04-09 14:30:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/694#issuecomment-611559456  

No problem. I'm glad everything is clear.
