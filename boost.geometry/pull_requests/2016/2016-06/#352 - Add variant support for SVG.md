# #352 Add variant support for SVG [Merged]

> Username: awulkiew  
> Created at: 2016-06-15 14:43:36 UTC  
> Updated at: 2016-06-23 12:36:38 UTC  
> Merged at: 2016-06-23 12:36:29 UTC  
> Closed at: 2016-06-23 12:36:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/352  

Move the code, in particular dispatches of both single and multi geometries, into one file `io/svg/write.hpp` for consistency with other IOs. The old files are left for backward compatibility.

---
