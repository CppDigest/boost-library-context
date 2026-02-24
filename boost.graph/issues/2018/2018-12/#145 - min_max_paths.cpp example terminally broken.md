# #145 - min_max_paths.cpp example terminally broken [Open]

> Username: jzmaddock  
> Created at: 2018-12-16 18:57:35 UTC  
> Updated at: 2018-12-16 18:57:35 UTC  
> Url: https://github.com/boostorg/graph/issues/145  

The example starts with:  
```  
#error "This example appears to be incorrect; it uses edge weights that are smaller than 0 using the comparison operator passed to Dijkstra's algorithm, which is not allowed."  
```
