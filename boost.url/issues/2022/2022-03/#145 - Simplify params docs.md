# #145 - Simplify params docs [Closed]

> Username: alandefreitas  
> Created at: 2022-03-18 17:19:52 UTC  
> Updated at: 2022-03-22 19:59:58 UTC  
> Closed at: 2022-03-22 19:59:58 UTC  
> Url: https://github.com/boostorg/url/issues/145  

Remove note as the only text in descriptions. Example:  
  
```  
 /** A read-only structure representing a decoded query parameter.  
  
        @note Both @ref params::reference and @ref params::const_reference are  
        read only.  
  
    */  
    using const_reference = reference;  
```  
  
Remove unused javadocs. Example:  
  
```  
    /** <!-- @name Members -->  
        <!-- @{ -->  
     */  
```  
  
Remove unnecessary refs. Example:  
  
```  
 /** Assignment from initializer list  
  
        Assigns @ref params from a list of @ref params::value_type.  
  
        Each instance of @ref params::value_type is a view of a query <key, value> pair.  
  
        @return Reference to this instance of @ref params  
  
        @param init Initializer list with query parameters  
  
     */  
```  
  
Describe that we do not support ranges of self-referencing strings. Example:  
  
```  
template<class FwdIt>  
    void  
    assign(FwdIt first, FwdIt last);  
```

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-03-18 17:21:48 UTC  
> Url: https://github.com/boostorg/url/issues/145#issuecomment-1072624616  

This text needs work:  
  
```  
        Each instance of @ref params::value_type is a view of a query <key, value> pair.  
```  
  
And this  
  
```  
       @return Reference to this instance of @ref params  
```  
  
you can just say   
  
```  
@return `*this`  
```

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-03-18 17:23:33 UTC  
> Url: https://github.com/boostorg/url/issues/145#issuecomment-1072625950  

Simplify briefs. Example:  
  
from   
  
```  
    /** Assignment from initializer list  
```  
  
to   
  
```  
/** Assignment  
```  
  
Because the docs show the signature

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-03-18 17:24:01 UTC  
> Url: https://github.com/boostorg/url/issues/145#issuecomment-1072626779  

Remove angle brackets from javadocs. Example:  
  
```  
 Each instance of @ref params::value_type is a view of a query <key, value> pair.  
```  
  
it can confuse the toolchain

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-03-18 17:24:45 UTC  
> Url: https://github.com/boostorg/url/issues/145#issuecomment-1072628541  

```  
        Assign @ref params from a range of @ref params::value_type.  
  
        Each instance of @ref params::value_type is a view of a query <key, value> pair.  
```  
  
could just say "The query parameters are replaced with the specified range." But you also need to note that it offers the strong exception safety guarantee

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-03-18 17:50:40 UTC  
> Url: https://github.com/boostorg/url/issues/145#issuecomment-1072651961  

You don't need to document template parameters which are deduced  
  
```  
        @tparam FwdIt Forward Iterator Type  
```

---

## Comment 6

> Username: alandefreitas  
> Created at: 2022-03-18 18:36:14 UTC  
> Updated at: 2022-03-18 18:39:03 UTC  
> Url: https://github.com/boostorg/url/issues/145#issuecomment-1072695261  

documenting of BOOST_XXX_NO_LIB  
  
```  
To use Boost.XXX as header-only; that is, to eliminate the requirement to link a program to a static or dynamic Boost.XXX library, place the following line in **exactly one** new or existing source file in your project.  
  
#include <boost/XXXX/src.hpp>  
  
[Note]	Note  
The "header-only" configuration needs BOOST_XXX_NO_LIB defined when building with compilers supporting auto-linking, such as Microsoft Visual C++. This macro will instruct Boost to deactivate auto-linking on MSVC.  
  
#define BOOST_XXX_NO_LIB  
#include <boost/XXX.hpp>  
```

---

## Comment 7

> Username: alandefreitas  
> Created at: 2022-03-21 16:55:07 UTC  
> Url: https://github.com/boostorg/url/issues/145#issuecomment-1074156772  

```  
Memory for allocated strings uses the allocator specified at construction of the container.  
```  
  
should read  
  
```  
Memory for allocated strings uses the allocator specified *upon* construction of the container.  
```  
  
The description needs to say what kind of iterator it is, what the value type looks like, https://master.url.cpp.al/url/ref/boost__urls__params/iterator.html
