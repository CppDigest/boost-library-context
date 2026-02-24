# #652 - atr_ missing from member initialization list [Closed]

> Username: mpnunez  
> Created at: 2022-05-27 01:21:52 UTC  
> Updated at: 2022-06-04 17:49:32 UTC  
> Closed at: 2022-06-04 17:49:32 UTC  
> Url: https://github.com/boostorg/boost/issues/652  

In boost/polygon/transform.hpp, the axis_transformation class has several constructors. atr_ is in the member initializer list for some, but not all. This is causing compiler warnings when I compile with the -Weffc++ flag. It could probably be initialized as `atr_(NULL_TRANSFORM)` everywhere.

---

## Comment 1

> Username: mpnunez  
> Created at: 2022-06-04 17:49:32 UTC  
> Url: https://github.com/boostorg/boost/issues/652#issuecomment-1146658550  

Moved to polygon project.
