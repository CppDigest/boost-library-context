# #42 - msvc: `BOOST_PP_VARIADIC_SIZE` broken for size larger than 64 [Open]

> Username: timblechmann  
> Created at: 2022-01-06 06:41:42 UTC  
> Updated at: 2022-01-06 06:41:42 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/42  

```  
#define BOOST_PP_LIMIT_VARIADIC 256  
  
#include <boost/preprocessor/variadic/size.hpp>  
  
#include <cstddef>  
  
#define VAR_DATA a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, b, b, b, b, b  
  
constexpr size_t sz = BOOST_PP_VARIADIC_SIZE(VAR_DATA);  
  
static_assert(sz == 65);  
```  
  
gives:  
```  
  
<source>(9): error C2065: 'b': undeclared identifier  
<source>(9): error C2131: expression did not evaluate to a constant  
<source>(9): note: a non-constant (sub-)expression was encountered  
<source>(11): error C2131: expression did not evaluate to a constant  
<source>(9): note: a non-constant (sub-)expression was encountered  
```  
  
compare: https://godbolt.org/z/bfoETMnad
