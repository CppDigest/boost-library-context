# #45 - Parsing to user-defined types doesn't work [Closed]

> Username: akrzemi1  
> Created at: 2024-01-04 00:11:38 UTC  
> Updated at: 2024-01-04 01:27:20 UTC  
> Closed at: 2024-01-04 01:27:20 UTC  
> Url: https://github.com/boostorg/parser/issues/45  

I expected the following to compile and to parse into my variable `x`:  
  
```c++  
#include <boost/hana/adapt_struct.hpp>  
#include <boost/parser/parser.hpp>  
  
struct X  
{  
  char a;  
  int b;  
};  
  
BOOST_HANA_ADAPT_STRUCT(X, a, b);  
  
int main()  
{  
  namespace bp = boost::parser;  
  auto pair = bp::char_ >> bp::int_;  
  
  X x;  
  bool b = bp::parse("d 3", pair, bp::ws, x);  
}  
```  
But it doesn't compile in GCC.  
  
A similar program in Boost.Spirit does the job:  
  
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
  
  X x;  
  std::string input = "d 3";  
  auto begin = input.begin();  
  bool b = bp::phrase_parse(begin, input.end(), pair, bp::ascii::space, x);  
}  
  
```

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-01-04 01:26:57 UTC  
> Url: https://github.com/boostorg/parser/issues/45#issuecomment-1876186009  

I know that this looks like a separate issue, and it kind if is, but I'm working on changes now that will address this, along with #28.  So, I'm closing this as a dupe of #28.
