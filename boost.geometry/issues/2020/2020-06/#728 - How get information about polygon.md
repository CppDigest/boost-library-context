# #728 - How get information about polygon? [Closed]

> Username: 0xtrzy  
> Created at: 2020-06-18 12:04:11 UTC  
> Updated at: 2020-07-14 22:45:03 UTC  
> Closed at: 2020-07-14 22:45:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/728  

How get information about:  
- number of points?  
- number of segments?  
- clockwise or not?  
  
this is my code https://pastebin.com/R9niv31u  
  
-----  
typedef boost::geometry::model::d2::point_xy<double> point_t;  
typedef boost::geometry::model::polygon<point_t> polygon_t;  
....  
this working:  
std::cout << "czy jest prosta?   " << (boost::geometry::is_simple(green) ? "tak" : "nie")   
this dont.  
(boost::geometry::clockwise(green)  
  
Meybe I must using Multi polygon?

---

## Comment 1

> Username: mloskot  
> Created at: 2020-06-18 12:11:53 UTC  
> Updated at: 2020-06-18 12:12:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/728#issuecomment-645977252  

The `boost::geometry::clockwise` is not a function, it's enumerator.  
  
https://www.boost.org/doc/libs/1_73_0/libs/geometry/doc/html/geometry/reference/enumerations/order_selector.html  
https://www.boost.org/doc/libs/1_73_0/libs/geometry/doc/html/geometry/reference/core/point_order.html  
  
1. Please, read the docs! Hint: `num_*`  
2. Please, try to write in English.  
3. Please, try to learn about how to format code blocks in Markdown.

---

## Comment 2

> Username: 0xtrzy  
> Created at: 2020-06-18 12:34:30 UTC  
> Updated at: 2020-06-18 12:40:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/728#issuecomment-645988863  

@mloskot yes, my english are ugly ;(   
  
but this example not showing how determining which polygon are clockwise or not.  
this only show possibility.  
  
in code exist green polygon.  
how check what is right or left?

---

## Comment 3

> Username: mloskot  
> Created at: 2020-06-18 12:40:04 UTC  
> Updated at: 2020-06-18 12:41:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/728#issuecomment-645991346  

It's a static property of a type:  
https://www.boost.org/doc/libs/1_73_0/libs/geometry/doc/html/geometry/reference/concepts/concept_ring.html  
https://www.boost.org/doc/libs/1_73_0/libs/geometry/doc/html/geometry/reference/concepts/concept_polygon.html  
  
> If the polygons underlying ring_type is defined as clockwise, the exterior ring must have the clockwise orientation, and any interior ring must be reversed w.r.t. the defined orientation (so: counter clockwise for clockwise exterior rings). If the ring_type is defined counter clockwise, it is vice versa.   
  
If you run `correct`, it will fix any unordered points, according to that type trait  
https://www.boost.org/doc/libs/1_73_0/libs/geometry/doc/html/geometry/reference/algorithms/correct/correct_1.html  
  
You may also try `is_valid` to see if the message will give you extra details on unordered points, I can't remember if it does  
https://www.boost.org/doc/libs/1_73_0/libs/geometry/doc/html/geometry/reference/algorithms/is_valid/is_valid_2_with_message.html

---

## Comment 4

> Username: 0xtrzy  
> Created at: 2020-06-18 12:45:36 UTC  
> Updated at: 2020-06-18 13:00:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/728#issuecomment-645994074  

@mloskot  num_points exist in my code too, but not working  
  
correct change whole polygon (with more than one shape)  
sometime are not a good idea. For example I have 'A' shape. I must have outer and hole inside. I need set exactly one shape to clockwise and other to not clockwise.  
in 59 line : ` boost::geometry::correct(blue);`  
  
is_valid exist in my code but show me a error? Why showing is not a valid? This is simple shape only 4 point.  
` boost::geometry::read_wkt("POLYGON((0 4, 3 1, 6 4, 3 0)(0 3, 0.1 3, 0.1 3.5, 0 3.5))", green);`  
but  
`std::cout << "is valid? " << (boost::geometry::is_valid(green) ? "tak" : "nie") << std::endl;  
`  
show no!

---

## Comment 5

> Username: mloskot  
> Created at: 2020-06-18 13:04:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/728#issuecomment-646003695  

If you read my https://github.com/boostorg/geometry/issues/728#issuecomment-645991346 carefully and used `is_valid` as I suggested, you would have answered your own question:  
  
  
```cpp  
#include <iostream>  
#include <string>  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <boost/geometry/io/wkt/wkt.hpp>  
  
typedef boost::geometry::model::d2::point_xy<double> point_t;  
typedef boost::geometry::model::polygon<point_t> polygon_t;  
  
int main()  
{  
    polygon_t green;  
    boost::geometry::read_wkt("POLYGON((0 4, 3 1, 6 4, 3 0)(0 3, 0.1 3, 0.1 3.5, 0 3.5))", green);    
  
    std::string msg;  
    std::cout << "is valid? " << boost::geometry::is_valid(green, msg) << std::endl;    
    std::cout << msg << std::endl;  
}  
```  
Outputs  
```  
is valid? 0  
Geometry is defined as closed but is open  
```  
Try it yourself https://godbolt.org/z/d-Zb9Y

---

## Comment 6

> Username: 0xtrzy  
> Created at: 2020-06-18 13:09:13 UTC  
> Updated at: 2020-06-18 13:11:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/728#issuecomment-646006121  

Yes, my code use this same way. in 59 line  
  
Question is how check one shape, (sub-polygon?)  
and how setup: clockwise or dont clockwise. For example char 'i' have 2 polygon. Dot and body. This is correct shape. But boost tell me is not valid. How check every element of polygons and meybe correct it?  
  
and number of points is in code but comment because not working.  
  
(prościej by było po polsku ;) może dasz się namówic na pocztę?)

---

## Comment 7

> Username: mloskot  
> Created at: 2020-06-18 13:16:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/728#issuecomment-646009958  

In the docs that I linked in https://github.com/boostorg/geometry/issues/728#issuecomment-645991346 about polygon and ring, you can read:  
  
> If the polygons underlying ring_type is defined as closed, all rings must be closed: the first point must be spatially equal to the last point.   
  
Rings of your polygons are not closed. A ring that is not closed is not a valid ring. Period.  
So, no, your "shape"-s are not correct according to concepts of Boost.Geometry. Period.

---

## Comment 8

> Username: 0xtrzy  
> Created at: 2020-06-18 13:20:37 UTC  
> Updated at: 2020-06-18 13:26:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/728#issuecomment-646012640  

@mloskot in first question I ask: Meybe I must using multi polygon?  
  
look at https://www.boost.org/doc/libs/1_57_0/libs/geometry/doc/html/geometry/reference/algorithms/difference.html  
green object have more than one shape.  
  
I have object similar chars 'A' 'i' 'Ń' etc. My polygon have more than one shape and holes. How join (union) two shapes? How setup hole? How set clockwise. And how get element in this shapes.   
  
? https://godbolt.org/z/TEoqFq

---

## Comment 9

> Username: mloskot  
> Created at: 2020-06-18 13:25:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/728#issuecomment-646015215  

Create polygon by creating rings one by one, one exterior and one or more interior rings:  
  
https://www.boost.org/doc/libs/1_73_0/libs/geometry/doc/html/geometry/reference/algorithms/append.html  
  
Run `correct` which should fix ordering of points in the rings.  
  
Once again, browse the docs, search for familiar names, discover the library. If you feel lazy, ask on StackOverflow.

---

## Comment 10

> Username: 0xtrzy  
> Created at: 2020-06-18 13:30:18 UTC  
> Updated at: 2020-06-18 13:30:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/728#issuecomment-646018170  

I'm not lazy, I 'm not undestand.   
  
I ask 3 question, You answer only on correct (this are not my question).  
  
No example how get point for example a[1] not working ?  
how turn polygon clockwise etc.  
and ... closing discusion. Please help me

---

## Comment 11

> Username: mloskot  
> Created at: 2020-06-18 13:42:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/728#issuecomment-646024812  

I did not say you are lazy. I said "if you feel lazy", as every one does from time to time...  
  
I did answer all your questions  
  
-  number of points?  
  
> Please, read the docs! Hint: `num_*`  
  
In other words, search for the function https://www.boost.org/doc/libs/1_73_0/libs/geometry/doc/html/geometry/reference/algorithms/num_points.html  
  
-  number of segments?  
  
Likewise, https://www.boost.org/doc/libs/1_73_0/libs/geometry/doc/html/geometry/reference/algorithms/num_segments.html  
  
-  clockwise or not?  
  
`point_order` to see what order types define, then `correct` to fix it, then you can assume you `point_order` reports valid order for your type.  
  
----  
  
You are asking basic questions which well covered by the library docs.  
Authors of the library put a lot of effort in the docs.   
Although there is lots of places where the docs could be improved, I don't think there is anything missing to answer your questions just by browsing and reading the docs.  
Basically, answer to your question boils down to RTFM!

---

## Comment 12

> Username: awulkiew  
> Created at: 2020-06-18 21:15:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/728#issuecomment-646309506  

Thanks Mateusz for handling this :)  
  
@0xtrzy The library gets the information about the order (Clockwise vs Counterclockwise) and Closedness (Last point equal to the first point or not) from the polygon type. The data stored inside a polygon as to be consistent with this type definition. See e.g.:  
https://www.boost.org/doc/libs/1_73_0/libs/geometry/doc/html/geometry/reference/models/model_polygon.html  
There are template parameters `ClockWise` and `Closed` defining this. And points stored in the polygon has to be consistent with these template parameters. Like that:  
```  
namespace bg = boost::geometry;  
typedef bg::model::point<double, 2, bg::cs::cartesian> point;  
bg::model::polygon<point, true, true> polygon_cw_closed;  
bg::model::polygon<point, true, false> polygon_cw_opened;  
bg::model::polygon<point, false, true> polygon_ccw_closed;  
bg::model::polygon<point, false, false> polygon_ccw_opened;  
  
boost::geometry::read_wkt("POLYGON((0 0, 0 3, 3 3, 3 0, 0 0)(1 1, 2 1, 2 2, 1 2, 1 1))", polygon_cw_closed);  
boost::geometry::read_wkt("POLYGON((0 0, 0 3, 3 3, 3 0)(1 1, 2 1, 2 2, 1 2))", polygon_cw_opened);  
boost::geometry::read_wkt("POLYGON((0 0, 3 0, 3 3, 0 3, 0 0)(1 1, 1 2, 2 2, 2 1, 1 1))", polygon_ccw_closed);  
boost::geometry::read_wkt("POLYGON((0 0, 3 0, 3 3, 0 3)(1 1, 1 2, 2 2, 2 1))", polygon_ccw_opened);  
```  
Note that interior rings has to have the opposite orientation than the exterior ring. Read about WKT representation: https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry  
  
If you're not sure what was the order of the points in WKT string you can make the data consistent with the template parameters by calling `bg::correct()`, e.g.:  
```  
// ccw, opened WKT but cw closed type  
boost::geometry::read_wkt("POLYGON((0 0, 3 0, 3 3, 0 3)(1 1, 1 2, 2 2, 2 1))", polygon_cw_closed);  
bg::correct(polygon_cw_closed);  
std::cout << bg::wkt(polygon_cw_closed) << std::endl;  
```  
In all cases `bg::is_valid(polygon_xxx)` should return true before you can pass it into any algorithm.  
  
You can also learn about the standard Boost.Geometry is conformant with:  
https://www.ogc.org/standards/sfa

---

## Comment 13

> Username: awulkiew  
> Created at: 2020-06-18 21:24:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/728#issuecomment-646313336  

Also note that `num_points` will return different value depending on the `Closed`/Opened` definition.   
  
Furthermore there is additional parameter in `num_points` defining whether or not the function should add 1 to the returned value for opened points in order to always get the same result whether or not the polygon is opened or not. If you pass `true` as the `add_for_open` you'll get the number of points as if the polygon was closed (so had rings with last point equal to the first one). See:  
https://www.boost.org/doc/libs/1_73_0/libs/geometry/doc/html/geometry/reference/algorithms/num_points.html  
  
And as Mateusz said, your polygon has to be valid. So call `bg::correct()` if you're not sure.

---

## Comment 14

> Username: mloskot  
> Created at: 2020-06-19 08:12:33 UTC  
> Updated at: 2020-06-19 14:54:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/728#issuecomment-646504366  

@0xtrzy Please, close the issue if your questions have been answered.  
  
p.s. @0xtrzy Wczoraj odpowiedziałem na twój e-mail p.t. `Subject: Re: założyłem drugi wątek` , ale dziś otrzymałem:  
  
```  
Delivery Status Notification (Delay)  
  
Odpowiedź otrzymana z serwera zdalnego:  451 BLAD SPF - zobacz strone / SPF Error: Please see http://www.openspf.org/Why?id=mateusz%40loskot.net&ip=209.85.218.67&receiver=smtp.wp.pl Jesli jestes uzytkownikiem poczta.wp.pl sprawdz poprawnosc autoryzacji SMTP / If you are a poczta.wp.pl user check SMTP configuration: http://poczta.wp.pl/autoryzacja/  
--  
  
Final-Recipient: rfc822; 0xtrzy@wp.pl  
Action: delayed  
Status: 4.0.0  
Remote-MTA: dns; mx.wp.pl. (212.77.101.4, the server for the domain wp.pl.)  
Diagnostic-Code: smtp; 451 BLAD SPF - zobacz strone / SPF Error: Please see http://www.openspf.org/Why?id=mateusz%40loskot.net&ip=209.85.218.67&receiver=smtp.wp.pl            Jesli jestes uzytkownikiem poczta.wp.pl sprawdz poprawnosc autoryzacji SMTP / If you are a poczta.wp.pl user check SMTP configuration: http://poczta.wp.pl/autoryzacja/  
Last-Attempt-Date: Fri, 19 Jun 2020 07:50:46 -0700 (PDT)  
Will-Retry-Until: Sun, 21 Jun 2020 07:13:37 -0700 (PDT)  
```  
  
Tak więc nie wiem czy otrzymasz moje odpowiedzi.
