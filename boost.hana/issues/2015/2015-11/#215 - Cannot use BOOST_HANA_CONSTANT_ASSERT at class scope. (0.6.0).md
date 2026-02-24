# #215 - Cannot use BOOST_HANA_CONSTANT_ASSERT at class scope. (0.6.0) [Closed]

> Username: russelltg  
> Created at: 2015-11-29 23:52:24 UTC  
> Updated at: 2015-12-18 20:03:56 UTC  
> Closed at: 2015-11-30 05:02:26 UTC  
> Url: https://github.com/boostorg/hana/issues/215  

Hey, I found a little bug. Clang (3.7.0) fails with error  
  
```  
main.cpp:5:5: error: 'auto' not allowed in non-static class member  
    BOOST_HANA_CONSTANT_ASSERT(boost::hana::bool_c<true>, "ERROR");  
  
```  
  
when using `BOOST_HANA_CONSTANT_ASSERT` at class scope.   
  
Steps to reproduce:  
  
compile this file:  
  
``` C++  
// main.cpp  
#include <boost/hana.hpp>  
  
class a  
{  
    BOOST_HANA_CONSTANT_ASSERT(boost::hana::bool_c<true>, "ERROR");  
};  
  
int main(){}  
  
```  
  
with this command:  
  
```  
clang++ -std=c++14 -stdlib=libc++ main.cpp -I"<path to boost hana 0.6.0>" -omain  
```  
  
Full compile log is attached.  
  
[log.txt](https://github.com/boostorg/hana/files/46803/log.txt)

---

## Comment 1

> Username: ldionne  
> Created at: 2015-11-29 23:55:33 UTC  
> Url: https://github.com/boostorg/hana/issues/215#issuecomment-160483919  

`BOOST_HANA_CONSTANT_ASSERT` & friends do not allow for an additional error message. I guess this should probably be supported.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-12-18 20:03:12 UTC  
> Url: https://github.com/boostorg/hana/issues/215#issuecomment-165885764  

@GuapoTaco I'm going to add `_MSG` versions of the assert macros that allow for a custom failure message. However, `BOOST_HANA_CONSTANT_ASSERT` may not be used at class scope, because of the way it is implemented. Well in theory you could use it at class scope, but it would add a member to your class, which you definitely don't want. It turns out that it's incredibly hard to come up with a general purpose macro that will work in all cases. It's fairly easy if you don't allow arbitrary variables to be used inside the conditional expression (e.g. `BOOST_MPL_ASSERT`), but it's much harder for us because the macro should work with local variables and stuff like that.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-12-18 20:03:56 UTC  
> Url: https://github.com/boostorg/hana/issues/215#issuecomment-165885917  

Basically, for class scope, I would say use `static_assert(decltype(condition)::value, "");` if the `condition` is a `Constant`.
