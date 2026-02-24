# #75 - An exception "cannot dereference end map/set iterator" in polygon_arbitrary_formation.hpp [Open]

> Username: anthonyyan2001  
> Created at: 2022-04-26 15:42:16 UTC  
> Updated at: 2022-04-28 04:07:17 UTC  
> Url: https://github.com/boostorg/polygon/issues/75  

While using Polygon Set Data Object void get(output_container& output) to get fractures of a complex polygon, line#1754 in polygon_arbitrary_formation.hpp caused the exception.  The exception happened since line#1687 in polygon_arbitrary_formation.hpp. In line#1687, it increases value of "iter" to get out "while" loop .  After the while loop, value of "iter" is null which is used in line#1754 to  cause the exception happened.
