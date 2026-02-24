# #166 - gcc-13 warning: unknown pragma regarding -Wundefined-var-template [Closed]

> Username: anarthal  
> Created at: 2024-03-21 22:42:12 UTC  
> Updated at: 2024-09-15 16:36:14 UTC  
> Closed at: 2024-09-15 16:36:14 UTC  
> Url: https://github.com/boostorg/pfr/issues/166  

`detail/fake_object.hpp` emits the following warning under gcc 13.1 (Linux):  
  
```  
./boost/pfr/detail/fake_object.hpp:23:35: error: unknown option after ‘#pragma GCC diagnostic’ kind [-Werror=pragmas]  
   23 | #   pragma GCC diagnostic ignored "-Wundefined-var-template"  
```  
  
You need to build with `-Wall` to get it. Actually, any code with the line `#   pragma GCC diagnostic ignored "-Wundefined-var-template"` emits such a warning. gcc-11 also emits the same warning. I don't have an exhaustive list of compilers, but it looks like GCC is not understanding this line.  
  
This has been introduced in Boost 1.85. I build with `-Wall -Wextra -Werror`, so these are a little bit annoying.
