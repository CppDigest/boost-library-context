# #57 - Should `lexeme` turn `tuple<char...>` into `string`? [Closed]

> Username: akrzemi1  
> Created at: 2024-01-08 21:27:37 UTC  
> Updated at: 2024-01-09 00:12:33 UTC  
> Closed at: 2024-01-09 00:12:33 UTC  
> Url: https://github.com/boostorg/parser/issues/57  

While I accept the rationale in https://github.com/tzlaine/parser/issues/54, I still expect that when I wrap a sequence of `char`s into a `lexeme`, I would get a `string`. Isn't "lexem" implying a one whole?  
  
Feel free to close it, if you disagree with my reasoning. The following is an example demonstrating this.  
  
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
#ifdef WITH_BOOST_SPIRIT  
  auto alnum = bp::ascii::alnum;  
  auto alpha = bp::alpha;  
#else  
  auto alnum = bp::ascii::alnum;  
  auto alpha = bp::ascii::alpha;  
#endif  
  
  auto myParser = bp::lexeme[(bp::ascii::alnum | bp::char_('_')) >> bp::ascii::digit];  
  
  std::string input = "m1";  
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
> Created at: 2024-01-09 00:12:33 UTC  
> Url: https://github.com/boostorg/parser/issues/57#issuecomment-1882020589  

I think this is now superseded by #55.
