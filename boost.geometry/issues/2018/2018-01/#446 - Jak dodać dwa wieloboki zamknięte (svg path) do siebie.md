# #446 - Jak dodać dwa wieloboki zamknięte (svg path) do siebie? [Closed]

> Username: xor256  
> Created at: 2018-01-05 10:48:14 UTC  
> Updated at: 2018-01-20 22:40:50 UTC  
> Closed at: 2018-01-20 22:40:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/446  

Polygon=Łamana zamknięta składa się nie tylko z punktów, ale i z odcinków beziera.

---

## Comment 1

> Username: mloskot  
> Created at: 2018-01-05 11:07:44 UTC  
> Updated at: 2018-01-05 11:08:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/446#issuecomment-355529974  

(English please, if possible)  
   
I don't think models for Bezier curves are supported or even planned.

---

## Comment 2

> Username: xor256  
> Created at: 2018-01-05 11:12:54 UTC  
> Updated at: 2018-01-05 12:32:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/446#issuecomment-355530935  

How to add (union) two closed polygons (svg path) to each other? (tłumaczenie z google translate)  
  
chcę zsumować np. dwie litery. Coś co jest opisywane w freetype jako tablica odcinków i krzywych beziera  
  
_mój angielski jest straszny i tylko bym kaleczył język Szekspira_

---

## Comment 3

> Username: awulkiew  
> Created at: 2018-01-05 23:51:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/446#issuecomment-355696423  

Bezier segments are not supported. What you can do now depends what you're trying to do exactly. You could find a different library supporting bezier segments. You could also convert each bezier segment to some number of linear segments (based on required precision). Then this geometry could be represented as Boost.Geometry Polygon and then you could calculate a union (see example here: http://www.boost.org/doc/libs/1_66_0/libs/geometry/doc/html/geometry/reference/algorithms/union_/union__3.html). However in Boost.Geometry there is no algorithm converting bezier curve to some number of linear segments (a Linestring) so you'd still have to find a different library for that or write your own algorithm.
