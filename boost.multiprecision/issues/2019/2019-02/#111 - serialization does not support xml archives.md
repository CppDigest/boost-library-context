# #111 - serialization does not support xml archives [Closed]

> Username: HDembinski  
> Created at: 2019-02-03 22:03:09 UTC  
> Updated at: 2019-02-13 13:38:22 UTC  
> Closed at: 2019-02-13 13:38:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/111  

I am using cpp_int in Boost.Histogram. @swatanabe asked me to support serialization to xml archives in his review. For that I need cpp_int to be xml-serializable. This looks like only text and binary archives are supported:  
https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/cpp_int/serialize.hpp  
Can this be implemented?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-02-05 18:24:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/111#issuecomment-460748025  

What do I need to do?  
  
I thought that once the type was serializable it was agnostic of the archive type?

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-02-06 14:17:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/111#issuecomment-461038169  

Basically, instead of `ar & my_variable` you need to do `ar & make_nvp("my_variable", my_variable)` everywhere. See https://www.boost.org/doc/libs/1_67_0/libs/serialization/doc/wrappers.html, section "Name-Value Pairs".

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-02-13 13:38:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/111#issuecomment-463202042  

Closing in favor of referenced PR.
