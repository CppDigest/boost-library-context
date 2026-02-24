# #869 - Sym difference may generate polygon with disconnected interior [Closed]

> Username: kleunen  
> Created at: 2021-06-18 08:58:23 UTC  
> Updated at: 2021-07-28 11:10:29 UTC  
> Closed at: 2021-07-28 11:10:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/869  

Please see this example:  
https://wandbox.org/permlink/Ib8ZrLmUlRI8UZEU  
  
````C++  
#include <iostream>  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <boost/geometry/geometries/multi_polygon.hpp>  
  
using point_t = boost::geometry::model::d2::point_xy<double>;  
using polygon_t = boost::geometry::model::polygon<point_t>;  
using ring_t = boost::geometry::model::ring<point_t>;  
using multi_polygon_t = boost::geometry::model::multi_polygon<polygon_t>;  
  
  
int main()  
{  
    multi_polygon_t a;  
    multi_polygon_t b;  
    multi_polygon_t c;  
      
    boost::geometry::read_wkt("MULTIPOLYGON(((70 50,70 10,10 10,10 70,50 70,50 90,70 90,70 70,90 70,90 50,70 50)))", a);  
    boost::geometry::read_wkt("MULTIPOLYGON(((50 50,50 70,70 70,70 50,50 50)))", b);  
    boost::geometry::read_wkt("MULTIPOLYGON(((50 50,50 30,30 30,30 50,50 50)))", c);  
      
    multi_polygon_t result;  
    boost::geometry::sym_difference(b, c, result);  
    std::cout << boost::geometry::wkt(result) << std::endl;  
      
    multi_polygon_t result_2;  
    boost::geometry::sym_difference(result, a, result_2);  
      
    std::string message;  
    if(!boost::geometry::is_valid(result_2, message))  
        std::cout << "Geometry is not valid: " << message << std::endl;  
      
    std::cout << boost::geometry::wkt(result_2) << std::endl;  
    return 0;  
}  
````  
  
Expected output is a valid polygon, output is:   
Geometry is not valid: Geometry has disconnected interior  
MULTIPOLYGON(((50 70,50 90,70 90,70 70,90 70,90 50,70 50,70 10,10 10,10 70,50 70),(50 70,50 50,70 50,70 70,50 70),(50 50,30 50,30 30,50 30,50 50)))

---

## Comment 1

> Username: awulkiew  
> Created at: 2021-06-18 10:13:51 UTC  
> Updated at: 2021-06-18 10:18:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/869#issuecomment-863927712  

Thanks! I confirm this is a bug.  
  
Reduced issue is that the result of `difference()`:  
```  
multi_polygon_t a, b, c;  
bg::read_wkt("MULTIPOLYGON(((70 50,70 10,10 10,10 70,50 70,50 90,70 90,70 70,90 70,90 50,70 50)))", a);  
bg::read_wkt("MULTIPOLYGON(((50 50,50 70,70 70,70 50,50 50)),((50 50,50 30,30 30,30 50,50 50)))", b);  
bg::difference(a, b, c);  
```  
  
![obraz](https://user-images.githubusercontent.com/1226951/122544958-97928600-d02d-11eb-9f38-82991b995022.png)  
  
is a single polygon with two holes while it should've been 3 separate polygons.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2021-06-19 10:22:17 UTC  
> Updated at: 2021-06-19 11:44:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/869#issuecomment-864387023  

I agree with the fact that it is a bug. I remember that some polygons can have two valid representations, but probably not this one.  
  
http://www.gdmc.nl/publications/2004/Invalid_Valid_Clean_Polygons.pdf  
This is an (old) article describing validity in the OGC context. But maybe the rules are a bit stricter in the meantime. I like this part of the article _any point inside or on the boundary of the polygon can be reached through  the  interior  of  the  polygon  from  any  other  point  inside  the  polygon_ and that rule is not fulfilled here.  
  
I'll assign it to myself but I'm not sure how to fix it yet.  
  
(EDITED) ~~This is generated with difference, but most probably it will be a problem with the overlay itself, be it intersection, union or difference, because they are implemented using the same underlying traversals.~~  
  
The generation with difference is wrong, but with intersection and union (resulting in the same spatial geometry) is fine. This will make it (hopefully) a bit easier to fix, somewhere a decision should be the other way round. To be continued.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2021-06-19 10:34:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/869#issuecomment-864388171  

> Reduced issue is that the result of `difference()`:  
  
Thanks! Adding this testcase

---

## Comment 4

> Username: barendgehrels  
> Created at: 2021-06-30 13:43:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/869#issuecomment-871416286  

I've a working solution concept and will change it a bit, test it more, and create a PR next week

---

## Comment 5

> Username: barendgehrels  
> Created at: 2021-07-08 14:48:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/869#issuecomment-876503319  

> I've a working solution concept and will change it a bit, test it more, and create a PR next week  
  
In review: https://github.com/boostorg/geometry/pull/887

---

## Comment 6

> Username: kleunen  
> Created at: 2021-07-08 14:51:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/869#issuecomment-876505396  

Maybe bit unrelated question, but, how can I check out boost geometry only ?   
How do I build against boost geometry from git and keep the rest of normal boost ?

---

## Comment 7

> Username: awulkiew  
> Created at: 2021-07-08 15:55:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/869#issuecomment-876554741  

@kleunen https://github.com/boostorg/wiki/wiki/Getting-Started%3A-Overview

---

## Comment 8

> Username: barendgehrels  
> Created at: 2021-07-08 16:22:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/869#issuecomment-876575069  

>   
>   
> Maybe bit unrelated question, but, how can I check out boost geometry only ?  
> How do I build against boost geometry from git and keep the rest of normal boost ?  
  
See link Adam for the official way.  
Getting Boost.Geometry from git and making a symbolic link (instead of the shipped geometry) should also work.
