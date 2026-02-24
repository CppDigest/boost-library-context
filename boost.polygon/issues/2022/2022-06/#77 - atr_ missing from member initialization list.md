# #77 - atr_ missing from member initialization list [Open]

> Username: mpnunez  
> Created at: 2022-06-04 17:49:11 UTC  
> Updated at: 2022-06-04 17:49:11 UTC  
> Url: https://github.com/boostorg/polygon/issues/77  

In boost/polygon/transform.hpp, the axis_transformation class has several constructors. atr_ is in the member initializer list for some, but not all. This is causing compiler warnings when I compile with the -Weffc++ flag. It could probably be initialized as atr_(NULL_TRANSFORM) everywhere.
