# #9 Fix for MSVC [Merged]

> Username: MarcelRaad  
> Created at: 2015-06-06 15:45:56 UTC  
> Updated at: 2016-05-02 04:36:40 UTC  
> Merged at: 2016-05-01 21:29:14 UTC  
> Closed at: 2016-05-01 21:29:14 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/9  

MSVC doesn't know "and". It warns:  
hohberg_biconnected_components.hpp(166) : warning C4067: unexpected tokens following preprocessor directive - expected a newline  
hohberg_biconnected_components.hpp(201) : warning C4067: unexpected tokens following preprocessor directive - expected a newline

---
