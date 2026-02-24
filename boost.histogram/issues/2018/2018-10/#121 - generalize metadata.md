# #121 - generalize metadata [Closed]

> Username: HDembinski  
> Created at: 2018-10-15 14:37:24 UTC  
> Updated at: 2018-10-27 17:49:16 UTC  
> Closed at: 2018-10-27 17:49:16 UTC  
> Url: https://github.com/boostorg/histogram/issues/121  

Instead of using a string label for each axis, make the "metadata" class a template argument of the builtin axis types. This has many benefits.  
  
* Users who don't want metadata can supply an empty struct. The code can use empty base class optimization to that there is zero overhead in this case.  
* By using different empty structs, distinct types can be created for otherwise identical axes to represent different things in type-safe way at compile-time.  
* Users who want to attach other metadata to an axis can do so.
