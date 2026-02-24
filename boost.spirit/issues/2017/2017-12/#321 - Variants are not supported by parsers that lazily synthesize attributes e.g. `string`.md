# #321 - Variants are not supported by parsers that lazily synthesize attributes e.g. `string` [Open]

> Username: Kojoley  
> Created at: 2017-12-11 18:06:59 UTC  
> Updated at: 2020-06-04 14:32:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/321  

If parser lazily synthesize attribute (e.g. `string`) it actually exposes a pair of iterators that will be used to build an attribute inplace by `qi::assign_to`/`x3::move_to` call.  
  
But neither `x3::move_to(first, last, attr)` nor `qi::assign_to(first, last, attr)` support variants. So next code fails to compile while logically valid:  
  
```cpp  
#include <boost/spirit/include/qi.hpp>  
#include <boost/spirit/home/x3.hpp>  
#include <boost/variant.hpp>  
#include <string>  
  
int main()  
{  
    typedef boost::variant<int, std::string> attribute_type;  
#if X3  
    {  
        namespace x3 = boost::spirit::x3;  
        x3::rule<class r, attribute_type> r;  
        auto start = r = x3::string("foo");  
  
        // need to have a parse call to instantiate the templates  
        attribute_type a;  
        std::string s = "foo";  
        x3::parse(s.cbegin(), s.cend(), start, a);  
    }  
#else  
    {  
        namespace qi = boost::spirit::qi;  
        qi::rule<const char*, attribute_type()> r;  
        r = qi::string("foo");  
    }  
#endif  
    return 0;  
}  
```

---

## Comment 1

> Username: djowel  
> Created at: 2017-12-11 23:06:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/321#issuecomment-350888317  

Nods. Good catch!
