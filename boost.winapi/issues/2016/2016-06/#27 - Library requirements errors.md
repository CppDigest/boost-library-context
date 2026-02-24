# #27 - Library requirements errors. [Closed]

> Username: grafikrobot  
> Created at: 2016-06-17 18:29:31 UTC  
> Updated at: 2016-06-19 16:55:38 UTC  
> Closed at: 2016-06-19 16:55:38 UTC  
> Url: https://github.com/boostorg/winapi/issues/27  

====== BEGIN OUTPUT ======  
libs/winapi: error: file not found; Did not find [project-root]/index.html file. The file is required for all libraries. Redirection to HTML documentation.  
libs/winapi: error: directory not found; Missing [project-root]/doc directory. The [project-root]/doc directory is required for all libraries. Sources to build with and built documentation for the library. If the library needs to build documentation from non-HTML files this location must be buildable with Boost Build.  
libs/winapi: warning: file not found; Did not find [project-root]/include/boost/[library].h\* file. A single header for the library is suggested at [project-root]/include/boost/[library].h\* if the library does not have a header directory at [project-root]/include/boost/[library].  
libs/winapi: error: directory not found; Missing [project-root]/meta directory. The [project-root]/meta directory is required for all libraries.  
  
EXIT STATUS: 1  
====== END OUTPUT ======

---

## Comment 1

> Username: Lastique  
> Created at: 2016-06-19 16:55:38 UTC  
> Url: https://github.com/boostorg/winapi/issues/27#issuecomment-227007843  

Fixed everything except the warning. There is no single library header in `boost` and the library is intentionally placed under `boost/detail`.
