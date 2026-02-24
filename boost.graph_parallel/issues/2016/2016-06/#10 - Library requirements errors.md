# #10 - Library requirements errors. [Closed]

> Username: grafikrobot  
> Created at: 2016-06-17 17:32:06 UTC  
> Updated at: 2023-04-03 02:55:27 UTC  
> Closed at: 2023-04-03 02:55:27 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/10  

====== BEGIN OUTPUT ======  
libs/graph_parallel: warning: file not found; Did not find [project-root]/include/boost/[library].h\* file. A single header for the library is suggested at [project-root]/include/boost/[library].h\* if the library does not have a header directory at [project-root]/include/boost/[library].  
libs/graph_parallel: error: directory not found; Missing [project-root]/meta directory. The [project-root]/meta directory is required for all libraries.  
  
EXIT STATUS: 1  
====== END OUTPUT ======

---

## Comment 1

> Username: Belcourt  
> Created at: 2016-11-01 01:07:35 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/10#issuecomment-257465722  

Added the meta directory, still no single header at the top-level.
