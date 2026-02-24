# #55 - How to parse into string a small token with a constraining pattern? [Closed]

> Username: akrzemi1  
> Created at: 2024-01-08 18:26:17 UTC  
> Updated at: 2024-01-14 08:45:46 UTC  
> Closed at: 2024-01-14 08:45:46 UTC  
> Url: https://github.com/boostorg/parser/issues/55  

Boost.Spirit allowed the syntax for defining a parser that matched a token compliant with a simple pattern and had a `std::string as attribute:  
  
```c++  
auto myParser  = (alpha >> alpha >> -alnum);  
```  
  
Does Boost.Parser offer an equivalent?  
  
Here is a full code that compiles in Boost.Spirit but not in Boost.Parser. You already said Boost.Parser will not provide an identical syntax, so I am asking for an alternative.  
  
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
  
  auto myParser  = (alpha >> alpha >> -alnum);  
  
  std::string input = "MM2";  
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
> Created at: 2024-01-09 00:11:32 UTC  
> Url: https://github.com/boostorg/parser/issues/55#issuecomment-1882018746  

Remember the thing I said before on #50 (https://github.com/tzlaine/parser/issues/50#issuecomment-1879827991)?  I think that applies to my example of parsing `int`s, but far less so to parsing individual characters.  Just like I have as special case for `std::string` instead of `std::vector<char>` in repeat parsers, I'm now considering a special case for char types in sequence parsers.  
  
I have this in mind:  
  
1) The example above would produce a `std::string`.  
2) Introduce `separate[]` directive (which would apply only to sequence parsers) to disable combining, like how it would happen under 1), or in general, like it happens now for `*int_ >> int_`.  
3) Introduce `merge[]` (also only for sequence parsers) that would make the `int_ >> int_` attribute `std::vector<int>`.  
  
I'm still wrestling with the idea of including `char32_t`-attribute parsers in 1) and in the current `std::string` versus `std::vector` rule.  I'm leading toward inclusion; I think it's more natural not to have to worry about the distinction.
