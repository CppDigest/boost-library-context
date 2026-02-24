# #509 - R tree query hundreds of points which satisfy a condition [Closed]

> Username: dayexingyang  
> Created at: 2018-09-18 07:21:25 UTC  
> Updated at: 2018-11-15 02:21:40 UTC  
> Closed at: 2018-11-15 02:21:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/509  

I want to query the points of which distances between a line are less than a value( such as 2.0). But there are hundreds of candidate points. So I want to speed up the query with R tree.  I'm not sure if r tree can handle it and how to set it up. Could you provide some advices?  
Thank you!

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-09-30 22:11:05 UTC  
> Updated at: 2018-09-30 22:16:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/509#issuecomment-425756172  

Hi, so this would be a spatial query with line+distance defining area. You could create buffer/polygon of this line and pass it into spatial query but this would not be optimal. It would be better to pass some object holding a line and your distance into `bgi::intersects()`. When this predicate is passed into the `query()` internally `bg::intersects()` is called. So you'd need to overload this function 2 times. One for Box and your line/area type and second one for Point and line/area type. Internally you would call: `bg::distance(point_or_box, line) < distance`.

---

## Comment 2

> Username: awulkiew  
> Created at: 2018-09-30 22:13:42 UTC  
> Updated at: 2018-09-30 22:17:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/509#issuecomment-425756353  

Actually you'd probably need only one overload, since one type would be some generic Point or Box, the other one your line/area type and `bg::distance()` should work for both combinations: Point/Linestring and Box/Linestring.

---

## Comment 3

> Username: awulkiew  
> Created at: 2018-09-30 22:15:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/509#issuecomment-425756447  

See here: https://stackoverflow.com/questions/19490435/boost-geometry-spatial-query-shapes
