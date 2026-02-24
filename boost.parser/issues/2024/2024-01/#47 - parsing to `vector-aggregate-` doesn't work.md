# #47 - parsing to `vector<aggregate>` doesn't work [Closed]

> Username: akrzemi1  
> Created at: 2024-01-04 07:12:52 UTC  
> Updated at: 2024-01-05 01:32:02 UTC  
> Closed at: 2024-01-05 01:32:02 UTC  
> Url: https://github.com/boostorg/parser/issues/47  

While https://github.com/tzlaine/parser/issues/28 fixes the parsing into a single aggregate, parsing to a vector of aggregates still doesn't work.  
  
The following fails to compile.  
```c++  
#include <boost/parser/parser.hpp>  
#include <vector>  
  
struct X  
{  
  char a;  
  int b;  
};  
  
int main()  
{  
  namespace bp = boost::parser;  
  auto pair = bp::char_ >> bp::int_;  
  auto pairs = +pair;  
  
  std::vector<X> x;  
  auto b = bp::parse("d 3", pairs, bp::ws, x);    
}  
```  
  
The analogous code in Boost.Spirit works:  
  
```c++  
#include <boost/fusion/adapted/struct/adapt_struct.hpp>  
#include <boost/spirit/include/qi.hpp>  
  
struct X  
{  
  char a;  
  int b;  
};  
  
BOOST_FUSION_ADAPT_STRUCT(X, a, b);  
  
int main()  
{  
  namespace bp = boost::spirit::qi;  
  auto pair = bp::char_ >> bp::int_;  
  auto pairs = +pair;  
  
  std::vector<X> x;  
  std::string input = "d 3 c 4";  
  auto begin = input.begin();  
  bool b = bp::phrase_parse(begin, input.end(), pairs, bp::ascii::space, x);  
}  
```

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-01-04 07:40:18 UTC  
> Url: https://github.com/boostorg/parser/issues/47#issuecomment-1876625011  

Thanks.  I'll have a look.
