# #58 Fix labeled_graph constructors [Merged]

> Username: sehe  
> Created at: 2016-02-25 22:27:36 UTC  
> Updated at: 2016-04-24 02:08:52 UTC  
> Merged at: 2016-04-24 02:08:52 UTC  
> Closed at: 2016-04-24 02:08:52 UTC  
> Url: https://github.com/boostorg/graph/pull/58  

- Wraparound bug leads to out-of-bounds addressing.   
    
  Seems obvious that `> 0` was meant (seeing the comments).   
  Also, if `vertices_size_type` is unsigned - which it usually (always?) is - the loop condition was never false.  
- Finally, one constructor didn't properly initialize the graph property

---
