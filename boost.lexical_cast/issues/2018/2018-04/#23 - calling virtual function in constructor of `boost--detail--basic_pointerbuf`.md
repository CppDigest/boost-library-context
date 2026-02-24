# #23 - calling virtual function in constructor of `boost::detail::basic_pointerbuf` [Closed]

> Username: akrzemi1  
> Created at: 2018-04-17 13:20:01 UTC  
> Updated at: 2018-05-01 19:43:29 UTC  
> Closed at: 2018-05-01 19:43:29 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/23  

clang-tidy reports (while checking the code that uses `boost::lexical_cast`) a virtual function is called in the default constructor of class template `boost::detail::basic_pointerbuf`. (Function `setbuf()` is declared virtual in base class.) This might be an indication of a bug. If not, I would recommend using a different construct that avoids confusion, for instance declaring the function override `final` in newer versions of C++, or providing another function with different name but with the same semantics as the overriding `setbuf()`.  
  
Here's the link to the offending call: https://github.com/boostorg/lexical_cast/blob/develop/include/boost/detail/basic_pointerbuf.hpp#L42

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-05-01 19:43:29 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/23#issuecomment-385768708  

Fixed in https://github.com/boostorg/lexical_cast/commit/fb017f8b82617069fbd57ac248cde7590373b414 by directly specifying the call to this_type.
