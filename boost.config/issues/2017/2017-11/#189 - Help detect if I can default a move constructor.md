# #189 - Help detect if I can default a move constructor [Closed]

> Username: akrzemi1  
> Created at: 2017-11-06 09:28:39 UTC  
> Updated at: 2018-01-28 11:13:09 UTC  
> Closed at: 2018-01-28 11:13:09 UTC  
> Url: https://github.com/boostorg/config/issues/189  

The following program fails to compile on GCC 4.4 and MSVC 12:  
  
```c++  
#include <boost/config.hpp>  
  
#if (!defined BOOST_NO_CXX11_DEFAULTED_FUNCTIONS) && (!defined BOOST_NO_CXX11_RVALUE_REFERENCES)  
static_assert(true, "***");  
#else  
static_assert(false, "***");  
#endif  
  
struct X  
{  
    X(X&&) = default;  
};  
  
int main() {}  
```  
  
Here is a Wandbox demo: https://wandbox.org/permlink/X5m8jmU18Fvo2Y86  
  
Boost.Config detects support for rvalue references and for defaulted functions, but defaulting a move constructor as defaulted is still a compiler error. Can we define a config macro for that?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2017-12-12 18:22:05 UTC  
> Url: https://github.com/boostorg/config/issues/189#issuecomment-351140741  

Care to file a PR?  Instructions for adding new macros are here: http://www.boost.org/doc/libs/1_65_1/libs/config/doc/html/boost_config/guidelines_for_boost_authors.html#boost_config.guidelines_for_boost_authors.adding_new_defect_macros

---

## Comment 2

> Username: akrzemi1  
> Created at: 2018-01-04 00:13:11 UTC  
> Url: https://github.com/boostorg/config/issues/189#issuecomment-355164117  

Here we go: https://github.com/boostorg/config/pull/205

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-01-28 11:13:09 UTC  
> Url: https://github.com/boostorg/config/issues/189#issuecomment-361054925  

PR is merged (thanks) so closing down...
