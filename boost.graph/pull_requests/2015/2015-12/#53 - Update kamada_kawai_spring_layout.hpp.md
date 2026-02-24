# #53 Update kamada_kawai_spring_layout.hpp [Merged]

> Username: coderakki  
> Created at: 2015-12-17 18:49:35 UTC  
> Updated at: 2016-05-01 21:42:15 UTC  
> Merged at: 2016-05-01 21:42:15 UTC  
> Closed at: 2016-05-01 21:42:15 UTC  
> Url: https://github.com/boostorg/graph/pull/53  

//There was a bug in line 109   
matrix size should be 3x3 else it will give segmentation fault (index out of range).

---
