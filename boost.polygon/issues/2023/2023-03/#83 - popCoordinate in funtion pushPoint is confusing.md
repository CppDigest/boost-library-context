# #83 - popCoordinate in funtion pushPoint is confusing [Closed]

> Username: xshenf  
> Created at: 2023-03-14 08:53:38 UTC  
> Updated at: 2023-03-21 03:45:47 UTC  
> Closed at: 2023-03-21 03:45:47 UTC  
> Url: https://github.com/boostorg/polygon/issues/83  

https://github.com/boostorg/polygon/blob/8ba35b57c1436c4b36f7544aadd78c2b24acc7db/include/boost/polygon/detail/polygon_formation.hpp#L964-L975  
  
When collinear, this function returns the result after popCoordinate, which may lose a coordinate, and push nothing.
