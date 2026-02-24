# #54 - sequence of `ascii` parsers should render a string [Closed]

> Username: akrzemi1  
> Created at: 2024-01-07 20:35:23 UTC  
> Updated at: 2024-01-08 03:40:38 UTC  
> Closed at: 2024-01-08 03:40:37 UTC  
> Url: https://github.com/boostorg/parser/issues/54  

Another Parser vs Spirit comparison. Apparently, Boost.Spirit assigns to a sequence of `ascii` parsers a stirng-attribute.  
  
```c++  
#ifdef WITH_BOOST_SPIRIT  
#include <boost/fusion/adapted/struct/adapt_struct.hpp>  
#include <boost/spirit/include/qi.hpp>  
namespace bp = boost::spirit::qi;  
#else  
#include <boost/parser/parser.hpp>  
namespace bp = boost::parser;  
#endif  
  
int main()  
{  
  auto myParser = (bp::ascii::alnum >> bp::ascii::alnum);  
  
  std::string input = "MM";  
  std::string result;  
  
#ifdef WITH_BOOST_SPIRIT  
  auto begin = input.begin();  
  bool b = bp::phrase_parse(begin, input.end(), myParser, bp::ascii::space, result);  
#else  
  auto b = bp::parse(input, myParser, bp::ws, result);  
#endif  
  
  if (b)  
    std::cout << ":-) " << result << std::endl;  
  else  
    std::cout << ":-(" << std::endl;  
}  
```

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-01-08 03:40:38 UTC  
> Url: https://github.com/boostorg/parser/issues/54#issuecomment-1880344250  

Yeah.  That's because `ATTR(x) >> ATTR(x)` is `std::vector<ATTR(x)>` in the general case as far as I know.  The ascii parsers have `char` attributes, so they produce `std::vector<char>`, but there's a special case that makes that `std::string` instead.  
  
That has changed in my lib in the last week or so.  Remember #36?  I used to take adjacent attributes of any type `T` and give them the attribute type `std::vector<T>`.  I no longer do that.  There were technical reasons why, but moreover, If I write `int_ >> int_` I want two ints.  I did not say anything about an unbounded sequence of ints.  It just plain feels wrong to me that `int_ >> int_` should produce a `std::vector<int>` instead of a `std::tuple<int, int>`.
