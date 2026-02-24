# #743 - remove type aliases [Closed]

> Username: vinniefalco  
> Created at: 2023-04-24 19:06:56 UTC  
> Updated at: 2023-04-28 20:27:27 UTC  
> Closed at: 2023-04-28 20:27:27 UTC  
> Url: https://github.com/boostorg/url/issues/743  

we should get rid of our aliases for error_code, result, et. al. and just use them directly from system.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2023-04-27 20:26:57 UTC  
> Url: https://github.com/boostorg/url/issues/743#issuecomment-1526381982  

What happens to the headers themselves? Do we just delete `boost/url/string_view.hpp`, or leave it empty, or leave it with just `#include <boost/core/detail/string_view.hpp>`?

---

## Comment 2

> Username: alandefreitas  
> Created at: 2023-04-27 20:31:36 UTC  
> Url: https://github.com/boostorg/url/issues/743#issuecomment-1526391471  

Regardless of the answer to the first question, what about  
  
```cpp  
using string_view  
    BOOST_DEPRECATED("Use boost::core::string_view instead") =   
        boost::core::string_view;  
```  
  
for a few versions?
