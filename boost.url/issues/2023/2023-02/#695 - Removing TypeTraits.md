# #695 - Removing TypeTraits [Closed]

> Username: alandefreitas  
> Created at: 2023-02-22 20:52:05 UTC  
> Updated at: 2023-03-01 22:06:43 UTC  
> Closed at: 2023-03-01 22:06:43 UTC  
> Url: https://github.com/boostorg/url/issues/695  

Review the possibility of removing Boost.TypeTraits  
  
We currently have:  
  
```cpp  
#include <boost/type_traits/remove_cv.hpp>  
#include <boost/type_traits/copy_cv.hpp>  
```  
  
in `tuple.hpp` and  
  
```cpp  
#include <boost/type_traits/is_final.hpp>  
#include <boost/type_traits/type_with_alignment.hpp>  
```  
  
in `over_allocator.hpp`

---

## Comment 1

> Username: alandefreitas  
> Created at: 2023-03-01 22:06:42 UTC  
> Url: https://github.com/boostorg/url/issues/695#issuecomment-1450915003  

@vinniefalco I had a look at that and it seems like it's not worth it.  
  
- `config.hpp` would need to include `<type_traits>`  
- `config.hpp` would be polluted with 3 not-so-small snippets (not 4 because C++11 includes `remove_cv`)  
- `type_traits` would still be a transitive dependency of boost.url. It would just be at level 3 now.  
- `type_traits` is also a comparatively very small dependency.  
  
```  
# after removing references to type_traits in libs/url  
> python tools/boostdep/depinst/depinst.py url  
Installing: config, headers, ../tools/boost_install, ../tools/build, ../tools/cmake  
Installing: assert, system, align, static_assert, core, variant2, optional, mp11, config, throw_exception, container, filesystem, unordered, json  
Installing: winapi, type_traits, move, utility, predef, detail, intrusive, smart_ptr, iterator, io, container_hash, atomic, preprocessor, tuple, describe  
Installing: mpl, concept_check, conversion, function_types, fusion  
Installing: typeof, functional  
Installing: function  
Installing: bind  
```
