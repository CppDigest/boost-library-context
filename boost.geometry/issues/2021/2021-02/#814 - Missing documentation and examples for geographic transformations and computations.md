# #814 - Missing documentation and examples for geographic transformations and computations [Open]

> Username: jpo234  
> Created at: 2021-02-19 08:44:43 UTC  
> Updated at: 2022-10-19 12:25:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/814  

The functionality related to geographic conversions and calculations is currently very lacking. Some examples in the documentation would be very much appreciated. See my stackoverflow question "[using boost::geometry to convert from Latitude and longitude to UTM](https://stackoverflow.com/questions/66268072/using-boostgeometry-to-convert-from-latitude-and-longitude-to-utm)"

---

## Comment 1

> Username: saki7  
> Created at: 2021-03-09 23:46:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/814#issuecomment-794617494  

The answer of your StackOverflow post might compile, but I'm afraid that it is inappropriate.  
  
In general, you must first define (or find out) the exact geodetic definition which is used by your lon/lat coordinate data. At least, parameters like geodetic reference system, datum, ellipsoid, etc. must be provided. You can't really *convert* plain `<double, double>` points to other systems. When doing projections, it is almost always wrong to assume those arbitrary points are from valid system; you must define both source and destination projection parameters.  
  
If you are studying geography, you might already know what I'm saying.  
  
I recommend to watch this talk, "Spatial Reference Systems Transformations with Boost.Geometry," by Adam Wulkiewicz (at FOSDEM 2019): <https://www.youtube.com/watch?v=dA5zcnNJKDY>  
  
---  
  
I am personally looking forward to seeing the official documentation for `boost::geometry::srs`. As a hobby project I have once tried to figure out the usages by myself. I've found that especially these points were hard to understand:  
  
- The reason why we would need to project geographic coordinates to some other system at all  
  - Although this is a very basic domain-specific knowledge of geography, I'm expecting it to be covered in the basic section of the doc  
- The need to find out the geodetic reference system for my dataset, and how to tell that to srs classes  
- In the first place, what is PROJ4 project and how is it adopted to Boost.Geometry?  
  - What are the meanings and semantics of PROJ4 parameters? How do we pass them to Boost.Geometry projection?  
- Choosing between `static_esri`, `esri`, `static_epsg`, `epsg`, `proj4`  
- The relationship between projection classes and `boost::geometry::srs::transformation`  
- Converting geometries between two projections  
- Runtime performance benchmarks  
- Useful external links like <https://epsg.io/>  
- Mention Google's Web Mercator in the tutorial section  
  - Boost.Geometry does not have bundled definition of epsg:3857  
  - Web Mercator is the most popular protocol for web-based datasets; many online articles use it for sample programs.   
  - Many websites (e.g. <https://epsg.io/3857>) present wrong definition for Web Mercator; I believe both nadgrids and towgs84 must be included  
  
Some points are covered in the video above.

---

## Comment 2

> Username: jpo234  
> Created at: 2021-03-10 18:24:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/814#issuecomment-795865546  

>   
>   
> The answer of your StackOverflow post might compile, but I'm afraid that it is inappropriate.  
>   
  
I came to a similar conclusion and switched to GeographicLib (https://geographiclib.sourceforge.io/).
