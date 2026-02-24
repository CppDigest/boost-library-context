# #1178 - Invalid difference for multipolygon with touching polygons enclosing exterior [Open]

> Username: awulkiew  
> Created at: 2023-07-22 19:59:58 UTC  
> Updated at: 2023-07-22 20:00:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/1178  

If such multipolygon is subtracted the result is a polygon with touching holes enclosing interior. Such polygon is invalid: "Geometry has disconnected interior". The result should be two polygons, one with a hole and second one inside this hole.  
  
Tested with VS2022 (msvc-14.3), Boost.Geometry develop (1.83-beta1).  
  
Examples:  
  
----  
  
```  
A = "POLYGON((-1 -1,-1 9,9 9,9 -1,-1 -1))";  
B = "MULTIPOLYGON(((0 1,0 2,1 2,1 1,0 1)),"  
                 "((5 0,1 0,1 1,4 1,4 2,1 2,1 3,5 3,5 0)))";  
```  
The result of A\B is  
```  
"MULTIPOLYGON(((-1 -1,-1 9,9 9,9 -1,-1 -1),(0 1,1 1,1 2,0 2,0 1),(5 0,5 3,1 3,1 2,4 2,4 1,1 1,1 0,5 0)))"  
```  
![obraz](https://github.com/boostorg/geometry/assets/1226951/ddd3ae09-edce-4468-9309-1a1a0833f38d)  
  
----  
  
```  
A = "POLYGON((-1 -1,-1 9,9 9,9 -1,-1 -1))";  
B = "MULTIPOLYGON(((0 1,0 2,1 2,1 1,0 1)),"  
                 "((1 2,1 3,2 3,2 2,1 2)),"  
                 "((2 1, 2 2, 3 2, 3 1, 2 1)),"  
                 "((1 0, 1 1, 2 1, 2 0, 1 0)))";  
```  
The result of A\B is  
```  
"MULTIPOLYGON(((-1 -1,-1 9,9 9,9 -1,-1 -1),(0 1,1 1,1 2,0 2,0 1),(1 2,2 2,2 3,1 3,1 2),(2 1,3 1,3 2,2 2,2 1),(1 0,2 0,2 1,1 1,1 0)))"  
```  
![obraz](https://github.com/boostorg/geometry/assets/1226951/183ac167-a744-44ad-bdae-0483c9945705)
