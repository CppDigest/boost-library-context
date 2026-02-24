# #953 - boost::geometry::azimuth returns values with incorrect sign convention [Closed]

> Username: mark-grimes  
> Created at: 2021-12-18 15:04:18 UTC  
> Updated at: 2022-12-01 09:34:58 UTC  
> Closed at: 2022-12-01 09:34:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/953  

boost::geometry::azimuth returns the angle between two points relative to the vertical axis (i.e. "North").  The documentation doesn't state the sign convention but it links to the [PostGIS documentation](https://postgis.net/docs/ST_Azimuth.html) "with the same functionality".  This documentation states that:  
> ...North = 0; Northeast = π/4; East = π/2; Southeast = 3π/4; South = π; Southwest 5π/4; West = 3π/2; Northwest = 7π/4  
  
i.e. only returns positive values going clockwise from 0 to 2π.  
  
The following code:  
```cpp  
    namespace bg = boost::geometry;  
    using cartesian_point = bg::model::point<double, 2, bg::cs::cartesian>;  
    cartesian_point first, second;  
    bg::set<0>( first, 0 ); bg::set<1>( first, 0 );  
  
    bg::set<0>( second, 1 ); bg::set<1>( second, 1 );  
    printf( "Azimith NE is %f\n", boost::geometry::azimuth( first, second ) );  
    bg::set<0>( second, 1 ); bg::set<1>( second, -1 );  
    printf( "Azimith SE is %f\n", boost::geometry::azimuth( first, second ) );  
    bg::set<0>( second, -1 ); bg::set<1>( second, -1 );  
    printf( "Azimith SW is %f\n", boost::geometry::azimuth( first, second ) );  
    bg::set<0>( second, -1 ); bg::set<1>( second, 1 );  
    printf( "Azimith NW is %f\n", boost::geometry::azimuth( first, second ) );  
```  
gives the output  
```  
Azimith NE is 0.785398  
Azimith SE is 2.356194  
Azimith SW is -2.356194  
Azimith NW is -0.785398  
```  
i.e. return values between -π and +π.  
  
The function should either be fixed to match the documentation, or the documentation updated to match the actual functionality.  
  
Tested in boost 1.77.0 but I see no relevant notes in the changelog for 1.78.0.

---

## Comment 1

> Username: mark-grimes  
> Created at: 2021-12-18 15:10:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/953#issuecomment-997215634  

> The function should either be fixed to match the documentation, or the documentation updated to match the actual functionality.  
  
My preference is for fixing the function to match the current documentation, since that definition matches that used in most fields and peoples intuitive assumption from a traditional compass bearing (principle of least surprise).

---

## Comment 2

> Username: addy90  
> Created at: 2022-01-18 08:52:58 UTC  
> Updated at: 2022-01-18 08:53:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/953#issuecomment-1015194471  

Hmm as far as I understand, the azimuth is calculated by the following formula:  
https://github.com/boostorg/geometry/blob/716c7913657f8d4d6cac7cdc60dfc98e8b6d8b1f/include/boost/geometry/strategies/cartesian/azimuth.hpp#L76  
This is the formula that is the same as on Wikipedia for radians:  
https://en.wikipedia.org/wiki/Azimuth#In_cartography  
In PostGIS the following formula is used, we can see that the formula is different concerning the coordinates and that it transforms the value form the formula to be positive only:  
https://github.com/nextgis-borsch/postgis/blob/83dc438236081258252f3e6e14fe926034d276a9/liblwgeom/measures.c#L2466  
  
All this for the cartesian case as an example. I expected the formula result after what is written on Wikipedia without further looking into the documentation. I think performance-wise it is better to have it exactly as it is and fix the documentation to mention the negative values. PostGIS already transforms the value but this can also be done by the user if needed and it saves computational performance for people who don't need it. In my case for example, I don't need the values to be positive.  
  
I think concerning principle of least surprise, it is best to fix the documentation not to point to PostGIS and mention clearly that the raw formula is used. What can be done is to post an additional algorithm that transforms the value into the positive range same as in PostGIS. Users who need the value to be positive can use the example transform algorithm then on top of the azimuth method. This would be most flexible and best concerning performance. Just my opinion.

---

## Comment 3

> Username: awulkiew  
> Created at: 2022-01-18 16:53:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/953#issuecomment-1015614114  

I also think that we should rather add more information in the documentation. Mentioning PostGIS is ok but the difference should be clearly expressed.
