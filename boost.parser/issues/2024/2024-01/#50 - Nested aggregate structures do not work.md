# #50 - Nested aggregate structures do not work [Closed]

> Username: akrzemi1  
> Created at: 2024-01-05 12:08:23 UTC  
> Updated at: 2024-01-07 06:50:38 UTC  
> Closed at: 2024-01-07 06:50:38 UTC  
> Url: https://github.com/boostorg/parser/issues/50  

Sorry, one more. The following case where we have an indirectly nested aggregate doesn't compile:  
  
```c++  
#include <boost/parser/parser.hpp>  
#include <vector>  
  
struct X {  
  char a;  
  int b;  
};  
  
struct Y {  
  std::vector<X> x;  
  int c;  
};  
  
int main()  
{  
  namespace bp = boost::parser;  
  auto parse_x = bp::char_ >> bp::int_;  
  auto parse_y = +parse_x >> bp::int_;  
  
  Y y;  
  auto b = bp::parse("d 3 4", parse_y, bp::ws, y);  
}  
```  
  
The Boost.Spirit analogue compiles fine:  
  
```c++  
#include <boost/fusion/adapted/struct/adapt_struct.hpp>  
#include <boost/spirit/include/qi.hpp>  
  
struct X {  
  char a;  
  int b;  
};  
  
struct Y {  
  std::vector<X> x;  
  int c;  
};  
  
BOOST_FUSION_ADAPT_STRUCT(X, a, b);  
BOOST_FUSION_ADAPT_STRUCT(Y, x, c);  
  
int main()  
{  
  namespace bp = boost::spirit::qi;  
  auto parse_x = bp::char_ >> bp::int_;  
  auto parse_y = +parse_x >> bp::int_;  
  
  Y y;  
  std::string input = "d 3 4";  
  auto begin = input.begin();  
  bool b = bp::phrase_parse(begin, input.end(), parse_y, bp::ascii::space, y);  
}  
```

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-01-06 06:40:01 UTC  
> Url: https://github.com/boostorg/parser/issues/50#issuecomment-1879573974  

Don't be sorry!  I appreciate the early review.  I'll investigate this this weekend.

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-01-06 20:55:16 UTC  
> Url: https://github.com/boostorg/parser/issues/50#issuecomment-1879826399  

Even worse, this also does not work:  
  
```c++  
#include <boost/parser/parser.hpp>  
#include <vector>  
  
struct X {  
  char a;  
  int b;  
};  
  
struct SimplerY {  
  X x;  
  int c;  
};  
  
int main()  
{  
  namespace bp = boost::parser;  
  auto parse_x = bp::char_ >> bp::int_;  
  auto parse_y = parse_x >> bp::int_;  
  
  SimplerY y;  
  auto b = bp::parse("d 3 4", parse_y, bp::ws, y);  
}  
```  
  
I have some work to do it seems.

---

## Comment 3

> Username: tzlaine  
> Created at: 2024-01-06 21:01:31 UTC  
> Url: https://github.com/boostorg/parser/issues/50#issuecomment-1879827991  

Ok, after thinking about this a bit, there's no bug with my "simplified" case above.  When you write `parse_x >> bp::int_` as the definition of `parse_y`, the sequence-parser `parse_x` combines with `bp::int_`, just as if you wrote it all on the same line.  IOW, `parse_y` is equivalent to `bp::char_ >> bp::int_ >> bp::int_`.  When you then try to parse that into an object of type `Y`, it has to map the three elements the parse expects onto the two data members of `Y.

---

## Comment 4

> Username: akrzemi1  
> Created at: 2024-01-06 21:03:13 UTC  
> Url: https://github.com/boostorg/parser/issues/50#issuecomment-1879828523  

Correct. Boost.Spirit also rejects your simplified example.
