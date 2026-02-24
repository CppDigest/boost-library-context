# #13 Fix Trac #10932 Missing Include: Bind Placeholders [Merged]

> Username: ax3l  
> Created at: 2015-01-14 15:03:44 UTC  
> Updated at: 2015-03-27 07:50:05 UTC  
> Merged at: 2015-03-26 13:16:15 UTC  
> Closed at: 2015-03-26 13:16:15 UTC  
> Url: https://github.com/boostorg/python/pull/13  

This is a pull request for trac issue https://svn.boost.org/trac/boost/ticket/10932  
  
I am compiling Boost.Python with `nvcc/6.5`, `gcc/4.6.2`, `python/2.7.8` and `cmake/3.0.1` right now.  
  
As a side note, I modified the standard `FindPythonLibs.cmake` a bit to match the cuda_add_library calls.  
  
During compile, get an error  
  
```  
boost/python/exception_translator.hpp(22): error: identifier "_1" is undefined  
boost/python/exception_translator.hpp(22): error: identifier "_2" is undefined  
```  
  
due to a missing include:  
  
```  
#include <boost/bind/placeholders.hpp>  
```

---

## Comment 1

> Username: ax3l  
> Created_at: 2015-03-27 07:50:05 UTC  
> Url: https://github.com/boostorg/python/pull/13#issuecomment-86860440  

@stefanseefeld thx! :)

---
