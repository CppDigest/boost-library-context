# #377 - Polygon validation [Closed]

> Username: Ironholds  
> Created at: 2017-01-22 20:48:30 UTC  
> Updated at: 2017-04-04 14:48:14 UTC  
> Closed at: 2017-03-26 10:55:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/377  

boost::geometry's WKT-reading code accepts "POLYGON((2 1.3,2.4 1.7))" as a valid WKT string - and it is a valid *string* but is not a valid *polygon*, since polygons have to be closed objects under the WKT spec. Is there any way to have the code validate the polygons more strictly, either in WKT-reading or generally?

---

## Comment 1

> Username: Ironholds  
> Created at: 2017-02-02 05:42:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/377#issuecomment-276873517  

Nada?

---

## Comment 2

> Username: barendgehrels  
> Created at: 2017-03-26 10:55:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/377#issuecomment-289272512  

boost::geometry::is_valid checks validity of a polygon (if they are closed - and more).

---

## Comment 3

> Username: Ironholds  
> Created at: 2017-03-26 17:57:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/377#issuecomment-289302383  

Wonderful; thanks so much!
