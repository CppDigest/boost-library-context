# #115 - Syntax error in none_t.hpp [Closed]

> Username: pdimov  
> Created at: 2023-10-06 01:28:47 UTC  
> Updated at: 2023-10-06 07:58:31 UTC  
> Closed at: 2023-10-06 07:58:31 UTC  
> Url: https://github.com/boostorg/optional/issues/115  

```  
..\../boost/none_t.hpp:20:1: error: expected unqualified-id before '||' token  
   20 | || defined(BOOST_NO_CXX11_LAMBDAS) || defined(BOOST_NO_CXX11_DECLTYPE_N3276) || defined(BOOST_NO_CXX11_NOEXCEPT) || defined(BOOST_NO_CXX11_DELETED_FUNCTIONS) || defined(BOOST_NO_CXX11_DEFAULTED_FUNCTIONS) || defined(BOOST_NO_CXX11_DEFAULTED_MOVES) || defined(BOOST_NO_CXX11_EXPLICIT_CONVERSION_OPERATORS)  
      | ^~  
```  
Looks like this file isn't covered by tests?
