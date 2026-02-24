# #60 [voronoi] Fixed a bug with color_exterior() [Open]

> Username: eadf  
> Created at: 2021-03-20 20:19:48 UTC  
> Updated at: 2021-07-10 18:39:28 UTC  
> Url: https://github.com/boostorg/polygon/pull/60  

I found a situation where ```voronoi_visualizer.cpp``` would fail to mark some edges as external:  
  
```  
0  
19  
67035 16168 -51301 122269  
-51301 122269 -50598 120727  
-50598 120727 -56132 110391  
-56132 110391 -102080 102917  
-102080 102917 -112508 94666  
-112508 94666 -110974 81469  
-110974 81469 -83788 43709  
-83788 43709 -87201 32462  
-87201 32462 -130792 16168  
-130792 16168 -139396 6040  
-139396 6040 -135315 -6597  
-135315 -6597 -101213 -38399  
-101213 -38399 -102342 -49954  
-102342 -49954 -142015 -74507  
-142015 -74507 -148480 -86107  
-148480 -86107 -142015 -97715  
-142015 -97715 -102342 -122269  
-102342 -122269 148479 -56855  
148479 -56855 67035 16168  
```  
  
The problem is that ```color_exterior()``` sometimes starts with a terminating edge ```v1()==NULL``` and marks that edge  and the twin as external.  
When ```color_exterior()``` is called again, this time on the twin edge that is leading away from infinity, it finds the edge already marked and terminates recursion.  
  
This could also be solved on the calling side:  
```  
  if (it->vertex1() != NULL && it->vertex0() == NULL) {  
      color_exterior(&(*it));  
  }  
```

---
