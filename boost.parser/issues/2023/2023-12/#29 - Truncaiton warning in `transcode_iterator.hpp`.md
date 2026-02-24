# #29 - Truncaiton warning in `transcode_iterator.hpp` [Closed]

> Username: akrzemi1  
> Created at: 2023-12-29 13:22:36 UTC  
> Updated at: 2023-12-29 20:06:30 UTC  
> Closed at: 2023-12-29 20:06:30 UTC  
> Url: https://github.com/boostorg/parser/issues/29  

On MSVC I get warning C4244 when compiling line  
https://github.com/tzlaine/parser/blob/master/include/boost/parser/detail/text/transcode_iterator.hpp#L3147  
due to conversion from `ptrdiff_t` do `uint8_t`.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2023-12-29 13:26:07 UTC  
> Url: https://github.com/boostorg/parser/issues/29#issuecomment-1872087011  

Similarly in  
https://github.com/tzlaine/parser/blob/master/include/boost/parser/detail/text/transcode_iterator.hpp#L2854  
  
This happens when compiling example https://github.com/tzlaine/parser/blob/master/example/hello.cpp

---

## Comment 2

> Username: tzlaine  
> Created at: 2023-12-29 19:52:40 UTC  
> Url: https://github.com/boostorg/parser/issues/29#issuecomment-1872307601  

The fixes for this are harmless, so I've implemented them.  However, neither of these warnings is legit.  In both cases, you know either know statically (the second one), or from the type's invariants (the first one) that the value will always be representable in the assignee.  This is why I always aim for warning-free GCC and Clang builds, and then basically ignore hte MSVC warnings. :)
