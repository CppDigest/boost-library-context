# #163 - Issue #157 is still not merged to master [Open]

> Username: adentinger  
> Created at: 2026-01-22 22:10:23 UTC  
> Updated at: 2026-01-26 20:54:16 UTC  
> Url: https://github.com/boostorg/range/issues/163  

Issue #157 resolves a compile error that's been on boost-range for since 1.88. Unfortunately despite the fix having been made in April 2025, Boost.Range 1.90.0 (installed via up-to-date vcpkg) still doesn't have it:  
  
```c++  
// boost/range/detail/any_iterator_interface.hpp  
// ...  
  
#include <boost/mpl/if.hpp>  
#include <boost/range/detail/any_iterator_buffer.hpp>  
#include <boost/iterator/iterator_categories.hpp>  
// add_const.hpp is still missing here  
#include <boost/type_traits/is_convertible.hpp>  
#include <boost/type_traits/is_reference.hpp>  
#include <boost/type_traits/remove_const.hpp>  
#include <boost/type_traits/remove_reference.hpp>  
```  
  
It looks like the commit that fixes this (9ac89e9936b826c13e90611cb9a81a7aa0508d20) isn't on `master` yet. Could that be put in the next release? Pretty please? ^_^ Issues #157, #159, #161, and now this one, are all about that same compile error.
