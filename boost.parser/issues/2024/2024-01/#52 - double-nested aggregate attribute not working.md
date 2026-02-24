# #52 - double-nested aggregate attribute not working [Closed]

> Username: akrzemi1  
> Created at: 2024-01-07 13:23:02 UTC  
> Updated at: 2024-01-07 18:36:32 UTC  
> Closed at: 2024-01-07 18:36:32 UTC  
> Url: https://github.com/boostorg/parser/issues/52  

The following example works with Boost.Spirit but not with Boost.Parser.  
  
```c++  
#include <vector>  
  
#ifdef WITH_BOOST_SPIRIT  
#include <boost/fusion/adapted/struct/adapt_struct.hpp>  
#include <boost/spirit/include/qi.hpp>  
namespace bp = boost::spirit::qi;  
#else  
#include <boost/parser/parser.hpp>  
namespace bp = boost::parser;  
#endif  
  
struct X {  
  char a;  
  int b;  
};  
  
struct Y {  
  std::vector<X> x;  
  int c;  
};  
  
struct Z {  
  std::vector<Y> y;  
  int d;  
};  
  
#ifdef WITH_BOOST_SPIRIT  
BOOST_FUSION_ADAPT_STRUCT(X, a, b);  
BOOST_FUSION_ADAPT_STRUCT(Y, x, c);  
BOOST_FUSION_ADAPT_STRUCT(Z, y, d);  
#endif  
  
int main()  
{  
  auto parse_x = bp::char_ >> bp::int_;  
  auto parse_y = +parse_x >> bp::int_;  
  auto parse_z = +parse_y >> bp::char_;  
  
  Z z;  
  std::string input = "d 2 3 c";  
  
#ifdef WITH_BOOST_SPIRIT  
  auto begin = input.begin();  
  bool b = bp::phrase_parse(begin, input.end(), parse_z, bp::ascii::space, z);  
#else  
  auto b = bp::parse(input, parse_z, bp::ws, z);  
#endif  
  
  if (b)  
    std::cout << ":-)" << std::endl;  
  else  
    std::cout << ":-(" << std::endl;  
}  
  
```

---

## Comment 1

> Username: akrzemi1  
> Created at: 2024-01-07 13:29:29 UTC  
> Url: https://github.com/boostorg/parser/issues/52#issuecomment-1880060663  

My next PR will be triple-nested attribute. I am not sure how costly (in terms of runtime performance) this is, but this is based on my real parser, and it used to work in Boost.Spirit.

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-01-07 18:32:48 UTC  
> Url: https://github.com/boostorg/parser/issues/52#issuecomment-1880136379  

Thanks again for all the great reports, including the reduced code.  
  
I just pushed a fix.  It also tests the triple-nested case.  I don't expect this to have any runtime cost at all.
