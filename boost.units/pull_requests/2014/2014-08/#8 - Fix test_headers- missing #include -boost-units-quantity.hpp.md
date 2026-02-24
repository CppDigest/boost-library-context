# #8 Fix test_headers: missing #include <boost/units/quantity.hpp> [Merged]

> Username: mkurdej  
> Created at: 2014-08-28 09:50:31 UTC  
> Updated at: 2014-09-10 19:17:08 UTC  
> Merged at: 2014-09-08 13:09:52 UTC  
> Closed at: 2014-09-08 13:09:52 UTC  
> Url: https://github.com/boostorg/units/pull/8  

`#include <boost/units/quantity.hpp>` was missing in codata headers (not self-contained).  
  
Move quantity template class default argument from `quantity.hpp` to `units_fwd.hpp`.  
It may be necessary to do the same for other forward declarations.

---
