# #10 - Library requirements errors. [Closed]

> Username: grafikrobot  
> Created at: 2016-06-17 17:26:11 UTC  
> Updated at: 2020-04-12 21:59:45 UTC  
> Closed at: 2020-04-12 21:59:45 UTC  
> Url: https://github.com/boostorg/detail/issues/10  

====== BEGIN OUTPUT ======  
libs/detail: error: file not found; Did not find [project-root]/index.html file. The file is required for all libraries. Redirection to HTML documentation.  
libs/detail: error: directory not found; Missing [project-root]/doc directory. The [project-root]/doc directory is required for all libraries. Sources to build with and built documentation for the library. If the library needs to build documentation from non-HTML files this location must be buildable with Boost Build.  
libs/detail: warning: file found; Found extra files in [project-root]/include/boost directory.  
libs/detail: error: directory not found; Missing [project-root]/meta directory. The [project-root]/meta directory is required for all libraries.  
  
EXIT STATUS: 1  
====== END OUTPUT ======

---

## Comment 1

> Username: Lastique  
> Created at: 2020-04-12 21:59:45 UTC  
> Url: https://github.com/boostorg/detail/issues/10#issuecomment-612682416  

I believe, all of the errors except "Found extra files in [project-root]/include/boost directory" are fixed. The "extra files in include/boost" are headers.
