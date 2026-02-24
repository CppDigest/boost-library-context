# #71 - Intersection of polygons with double coordinate type doesn't work [Open]

> Username: theoldestsc  
> Created at: 2021-11-17 11:49:11 UTC  
> Updated at: 2022-06-10 20:18:47 UTC  
> Url: https://github.com/boostorg/polygon/issues/71  

https://godbolt.org/z/v7exo37sP - this is an example of intersection custom polygons with a double data type.  
https://godbolt.org/z/9n8We6eqb - example with your polygons  
  
You can see that if i change coordinate type to double i can't get any intersections

---

## Comment 1

> Username: mpnunez  
> Created at: 2022-06-10 20:18:47 UTC  
> Url: https://github.com/boostorg/polygon/issues/71#issuecomment-1152707267  

Coordinate types need to be integral, not float: [https://www.boost.org/doc/libs/1_79_0/libs/polygon/doc/gtl_coordinate_concept.htm](https://www.boost.org/doc/libs/1_79_0/libs/polygon/doc/gtl_coordinate_concept.htm).
