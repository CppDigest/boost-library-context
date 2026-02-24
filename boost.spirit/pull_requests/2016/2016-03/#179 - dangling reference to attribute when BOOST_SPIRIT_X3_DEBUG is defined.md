# #179 dangling reference to attribute when BOOST_SPIRIT_X3_DEBUG is defined [Merged]

> Username: hia3  
> Created at: 2016-03-26 19:09:26 UTC  
> Updated at: 2016-03-27 10:50:50 UTC  
> Merged at: 2016-03-27 10:50:36 UTC  
> Closed at: 2016-03-27 10:50:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/179  

dbg_attribute_type(attr_) creates a temporary that is bound to const reference inside of context_debug.  
  
This will fail on assert:  
  
```  
#define BOOST_SPIRIT_X3_DEBUG  
  
#include <iostream>  
#include <boost/spirit/home/x3.hpp>  
  
namespace x3 = boost::spirit::x3;  
  
struct p  
{  
    bool alive = true;  
  
    void access() const  
    {  
        assert(alive);  
    }  
    ~p()  
    {  
        alive = false;  
    }  
};  
  
std::ostream & operator << (std::ostream & os, p o)  
{  
    o.access();  
    return os << "o";  
}  
  
x3::symbols<p> s  
{  
    {  
        { "s", p{} }  
    },  
    "s"  
};  
  
auto r = x3::rule<struct r, p>{ "r" } = s;  
  
int main()  
{  
    std::string str = "s";  
    auto b = str.begin();  
    auto e = str.end();  
  
    p v;  
  
    phrase_parse(b, e, r, x3::blank, v) && b == e;  
}  
```

---

## Comment 1

> Username: djowel  
> Created_at: 2016-03-26 22:17:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/179#issuecomment-201940750  

This is a critical bug. Could you please add this test case in the x3 test suite as well?

---

## Comment 2

> Username: hia3  
> Created_at: 2016-03-27 04:35:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/179#issuecomment-201991374  

Added test case gives me  
  
```  
<b>  
  <try>a</try>  
  <success></success>  
  <attributes>debug.cpp(43): test 'alive' failed in function 'void __thiscall my_attribute::access(void) const'  
my_attribute</attributes>  
</b>  
1 error detected.  
  
```

---

## Comment 3

> Username: djowel  
> Created_at: 2016-03-27 10:50:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/179#issuecomment-202033437  

Thanks!

---
