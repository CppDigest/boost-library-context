# #418 - warning: missing field 'tp_version_tag' initializer [-Wmissing-field-initializers] [Open]

> Username: marcosbento  
> Created at: 2023-06-20 12:00:19 UTC  
> Updated at: 2023-06-20 12:00:19 UTC  
> Url: https://github.com/boostorg/python/issues/418  

Hi,  
  
using Clang 14/15/16 (-Wall -Wpedantic -Wextra) with Boost 1.81.0 and Python 3.11, I get the following warning at   
  
```  
In file included from /my/project/Source.hpp:xx:  
In file included from /path/to/boost/1.81.0/include/boost/python.hpp:48:  
/path/to/boost/1.81.0/include/boost/python/opaque_pointer_converter.hpp:169:1: warning: missing field 'tp_version_tag' initializer [-Wmissing-field-initializers]  
```  
  
Having a closer look, it seems that when defining a `PyTypeObject`, at `.../include/boost/python/opaque_pointer_converter.hpp`, the code isn't considering the last few data members:  
  
```c++  
// from .../include/python3.11/cpython/object.h  
...  
struct _typeobject {  
...  
    PyObject *tp_weaklist;  
    destructor tp_del;  
  
    /* Type attribute cache version tag. Added in version 2.6 */  
    unsigned int tp_version_tag;  
  
    destructor tp_finalize;  
    vectorcallfunc tp_vectorcall;  
};  
...  
```  
  
```c++  
// from .../include/boost/python/opaque_pointer_converter.hpp  
...  
template <class Pointee>  
PyTypeObject opaque<Pointee>::type_object =  
{  
    ...  
    0,          /* tp_weaklist */  
#if PYTHON_API_VERSION >= 1012  
    0           /* tp_del */  
#endif  
};  
...  
```
