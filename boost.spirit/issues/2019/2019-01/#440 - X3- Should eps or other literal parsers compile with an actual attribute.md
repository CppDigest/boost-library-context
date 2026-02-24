# #440 - X3: Should eps or other literal parsers compile with an actual attribute [Open]

> Username: Kojoley  
> Created at: 2019-01-17 14:22:41 UTC  
> Updated at: 2020-06-07 21:09:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/440  

```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/include/vector.hpp>  
int main()  
{  
    using boost::spirit::x3::eps;  
    using boost::spirit::x3::lit;  
    char const* const it = "";  
    int i;  
    char c;  
    boost::fusion::vector<> v;  
  
    parse(it, it, eps, i);  
    parse(it, it, eps, c);  
      
    parse(it, it, lit('x'), i);  
    parse(it, it, lit('x'), c);  
  
    parse(it, it, eps >> eps, i);  
    parse(it, it, eps >> eps, c);  
    parse(it, it, eps >> eps, v);  
  
    parse(it, it, lit('x') >> lit('x'), i);  
    parse(it, it, lit('x') >> lit('x'), c);  
    parse(it, it, lit('x') >> lit('x'), v);  
}  
```  
From my point of view all those cases are wrong and ideally should not compile by firing a static assert.

---

## Comment 1

> Username: Kojoley  
> Created at: 2020-06-07 20:51:29 UTC  
> Updated at: 2020-06-07 21:09:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/440#issuecomment-640278024  

#608 made think about opposite issue in optional parser which has a sibling in alternative parser:  
```cpp  
#include <boost/spirit/home/x3.hpp>  
int main()  
{  
    using boost::spirit::x3::eps;  
    using boost::spirit::x3::int_;  
    char const* const it = "";  
    int i;  
    parse(it, it, int_ | eps, i); // equivalent of -int_ which also should not compile  
}  
```
