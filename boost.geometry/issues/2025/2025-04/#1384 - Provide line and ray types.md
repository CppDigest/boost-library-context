# #1384 - Provide line and ray types [Closed]

> Username: MarcelloPerathoner  
> Created at: 2025-04-13 11:58:08 UTC  
> Updated at: 2025-04-19 07:25:15 UTC  
> Closed at: 2025-04-19 07:25:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/1384  

Many geometric algorithms use a "line sweep" or "plane sweep" or a rotating "ray sweep". Eg. often used in polygon trapezoidation, Voronoi, visibility graph.  
  
Please provide these two data types:  
  
- line, extending infinitely in both directions,  
- ray, extending inifinitely in one direction only,  
  
and intersection algorithms for them.
