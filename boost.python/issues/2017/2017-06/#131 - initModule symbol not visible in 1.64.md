# #131 - initModule symbol not visible in 1.64 [Closed]

> Username: rosswhitfield  
> Created at: 2017-06-06 14:03:51 UTC  
> Updated at: 2017-10-25 00:19:00 UTC  
> Closed at: 2017-10-25 00:19:00 UTC  
> Url: https://github.com/boostorg/python/issues/131  

I'm compiling a project that sets the default visibility to `hidden` using `CMAKE_CXX_VISIBILITY_PRESET=hidden`. This worked in 1.63 as the `initModule` is set back to `default` [here](https://github.com/boostorg/python/blob/boost-1.63.0/include/boost/python/module_init.hpp#L75-L79), unfortunately after #1 `BOOST_PYTHON_USE_GCC_SYMBOL_VISIBILITY` is no longer defined so the visibility is not getting set back to `default`.  
  
So I'm getting errors like `ImportError: dynamic module does not define init function (init_kernel)` and the equivalent for python 3 `ImportError: dynamic module does not define module export function (PyInit__kernel)`

---

## Comment 1

> Username: lukaszforynski  
> Created at: 2017-08-22 14:14:52 UTC  
> Updated at: 2017-08-22 14:15:27 UTC  
> Url: https://github.com/boostorg/python/issues/131#issuecomment-324039652  

This seems to be the offending change..   
  
  
```C++  
include/boost/python/object/instance.hpp  
@@ -11,7 +11,7 @@  
   
 namespace boost { namespace python  
 {  
-  struct BOOST_PYTHON_DECL_FORWARD instance_holder;  
+  struct instance_holder;  
 }} // namespace boost::python  
   
 namespace boost { namespace python { namespace objects {   
```  
  
Changing it back (*to what was originally intended? in #1):  
  
```C++  
include/boost/python/object/instance.hpp  
@@ -11,7 +11,7 @@  
   
 namespace boost { namespace python  
 {  
-  struct instance_holder;  
+  struct BOOST_PYTHON_DECL instance_holder;  
  
 }} // namespace boost::python  
   
 namespace boost { namespace python { namespace objects {   
```  
  
Seems to fix the issue.
