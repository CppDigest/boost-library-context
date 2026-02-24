# #998 - In Boost v1.87 boost::geometry::intersection can fail if the two polygons share some identical vertices [Open]

> Username: sbarkby  
> Created at: 2025-01-15 06:52:52 UTC  
> Updated at: 2025-01-15 06:52:52 UTC  
> Url: https://github.com/boostorg/boost/issues/998  

Godbolt [example](https://godbolt.org/z/vP3MY7a6b)  
  
<img width="608" alt="Image" src="https://github.com/user-attachments/assets/18cd1728-6fdc-4f99-8fba-c93db0c044e3" />  
  
Two polygons are specified in the example above that almost completely overlap. However calling boost::geometry::intersection() on these two polygons in Boost v1.87 does not register as an intersection, whereas it gave the correct result before in Boost v.186 and earlier.  
  
I have found that adding a small 1e-12 perturbation to the vertices of one of the polygons in this example results in the intersection being correctly calculated in v.187, leading me to believe that the vulnerability here occurs when both polygons in the calculation share one or more identical vertices.
