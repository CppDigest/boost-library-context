# #14 Fix Trac #10933 incomplete type in "contains" [Closed]

> Username: ax3l  
> Created at: 2015-01-14 15:40:47 UTC  
> Updated at: 2018-04-09 08:03:17 UTC  
> Closed at: 2018-04-09 08:03:17 UTC  
> Url: https://github.com/boostorg/python/pull/14  

https://svn.boost.org/trac/boost/ticket/10933  
  
Hi,  
  
I am compiling Boost.Python with `nvcc/6.5`, `gcc/4.6.2`, `python/2.7.8` and `cmake/3.0.1` right now.  
  
As a side note, I modified the standard `FindPythonLibs.cmake` a bit to match the `cuda_add_library` calls.  
  
During compile, I get the error  
  
```  
boost/python/object_core.hpp(499): error: function "boost::python::api::object_operators<U>::attr(const char *) const" returns incomplete type "boost::python::api::const_object_attribute"  
```  
  
from `include/boost/python/object_core.hpp`  
  
```  
template <typename U>  
template <class T>  
object api::object_operators<U>::contains(T const& key) const  
{  
    return this->attr("__contains__")(object(key));  
}  
```  
  
Replacing the return statement with  
  
```  
    return (*this)[object(key)];  
```  
  
seems to compile and my modules work, but **I am not sure if the functionality is still ok since I have no idea what this function actually does** (nor how to test/trigger it).  
  
It would be great if we can fix that compile error and someone could give me a review.

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2018-04-09 01:16:52 UTC  
> Url: https://github.com/boostorg/python/pull/14#issuecomment-379600514  

Is this still an issue for current versions of `nvcc` ?

---

## Comment 2

> Username: ax3l  
> Created_at: 2018-04-09 08:03:17 UTC  
> Url: https://github.com/boostorg/python/pull/14#issuecomment-379668470  

Can't reproduce it with 8.0.44 from a quick test.

---
