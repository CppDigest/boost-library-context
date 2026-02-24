# #729 - Returning a parser from a function breaks it [Closed]

> Username: maliberty  
> Created at: 2022-07-25 18:48:47 UTC  
> Updated at: 2022-07-25 20:42:50 UTC  
> Closed at: 2022-07-25 19:09:00 UTC  
> Url: https://github.com/boostorg/spirit/issues/729  

Below is a test case that demonstrates parsing a simple syntax.  In one case I have a function returning a parser (called endcap) and in the other the parser is contained in a single function.  The former fails while the later works.  I can't sort out what the difference is and am hoping for some help.  
  
I'm using Boost 1.76 with gcc 8.3.1.  
  
```  
// g++ --std=c++17 -o parser parser.cpp && ./parser  
// gcc version 8.3.1 20190311 (Red Hat 8.3.1-3) (GCC)  
// Boost 1.76  
#include <boost/spirit/include/qi.hpp>  
#include <string_view>  
  
namespace qi = boost::spirit::qi;  
namespace ascii = boost::spirit::ascii;  
  
using boost::spirit::ascii::space_type;  
using boost::spirit::qi::lit;  
  
using ascii::space;  
  
auto endcap() { return lit("ENDCAP") >> lit(";"); }  
  
template <typename Iterator> bool parse1(Iterator first, Iterator last) {  
  auto type_rule = (lit("CLASS") >> endcap());  
  
  bool valid = qi::phrase_parse(first, last, type_rule, space);  
  
  return valid && first == last;  
}  
  
////////// Same as above but the endcap parser is inlined into the caller  
template <typename Iterator> bool parse2(Iterator first, Iterator last) {  
  auto type_rule = (lit("CLASS") >> lit("ENDCAP") >> lit(";"));  
  
  bool valid = qi::phrase_parse(first, last, type_rule, space);  
  
  return valid && first == last;  
}  
  
int main() {  
  std::string str("CLASS ENDCAP ;");  
  std::string_view s(str);  
  std::cout << parse1(s.begin(), s.end()) << '\n';  
  std::cout << parse2(s.begin(), s.end()) << '\n';  
}  
```  
when I run this I get:  
```  
0  
1  
```  
Thanks in advance for the help!

---

## Comment 1

> Username: Kojoley  
> Created at: 2022-07-25 19:08:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/729#issuecomment-1194500411  

Duplicate of #436  
  
Combining operators capture parsers by reference. You need to use [`qi::copy`](https://www.boost.org/doc/libs/1_79_0/libs/spirit/example/qi/typeof.cpp). Fixed repro https://wandbox.org/permlink/nio16CExgMHtVZ0f

---

## Comment 2

> Username: maliberty  
> Created at: 2022-07-25 20:42:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/729#issuecomment-1194604330  

Thanks very much for the quick help!
