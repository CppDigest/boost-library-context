# #97 - Merge Assert, StaticAssert, ThrowException into Core [Open]

> Username: Kojoley  
> Created at: 2021-10-16 16:45:07 UTC  
> Updated at: 2021-10-16 16:45:07 UTC  
> Url: https://github.com/boostorg/core/issues/97  

Recently Core grew with additional dependencies on StaticAssert (in 1.76) and ThrowException (in the next release), previously it was only depending on Assert and Config. That made a lot of libraries checkout the same four repositories, three of them containing only a few headers. Merging into a single dependency will affect mostly positive about 100 libraries and negative 1: StlInterfaces (depends on Assert, and will on Core).  
  
There is some difference in documentation format used (https://github.com/boostorg/core/commit/95924b1329da49e70c9c8485b87e066895db2d64#r58137316), but that should be solvable.  
  
Headers list:  
  
StaticAssert (depends on Config)  
- boost/static_assert.hpp  
  
Assert (depends on Config)  
- boost/assert.hpp  
- boost/current_function.hpp  
- boost/assert/source_location.hpp  
  
ThrowException (depends on Assert and Config)  
- boost/throw_exception.hpp  
- boost/exception/exception.hpp
