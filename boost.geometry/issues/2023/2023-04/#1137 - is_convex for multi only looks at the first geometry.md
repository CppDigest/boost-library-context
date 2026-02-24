# #1137 - is_convex for multi only looks at the first geometry [Open]

> Username: barendgehrels  
> Created at: 2023-04-19 17:43:52 UTC  
> Updated at: 2023-07-23 00:20:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/1137  

Commented with:  
```  
// TODO: this looks wrong, it should only return convex if all its rings are convex  
```

---

## Comment 1

> Username: vissarion  
> Created at: 2023-06-19 10:18:51 UTC  
> Updated at: 2023-06-19 10:19:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/1137#issuecomment-1596918301  

It depends on how we define `is_convex`. Some possible definitions:  
1. input equals its convex hull  
2. union of polygon parts of the multi-polygon is convex  
3. each polygon of the multi polygon is convex  
  
To my understanding the most natural definition is 1. In such a case the current implementation seems correct.   
For 2 the user can call union and then `is_convex` and for 3 the user can iterate over all polygons of a multipolygon and apply `is_convex`.  
  
What is missing is a documentation for `is_convex` where we should mention the definition that we choose so that the user (or the contributor) knows what to expect for such a function.
