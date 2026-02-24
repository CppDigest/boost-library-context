# #35 - Describe backtracking [Closed]

> Username: akrzemi1  
> Created at: 2023-12-29 21:30:11 UTC  
> Updated at: 2024-01-08 04:55:26 UTC  
> Closed at: 2024-01-08 04:55:26 UTC  
> Url: https://github.com/boostorg/parser/issues/35  

I only learned from the [JSON example](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/extended_examples/parsing_json.html) that there is a system for backtracking incomparible with Boost.Spirit.  
  
I am trying to port my parser code from Boost.Spirit X3 to Boost.Parser and stumbled upon the problem, how to rewrite the usage of `boost::spirit::qi::hold`. Boost.Spirit X3 has backtracking disabled by default -- for parsing performance, I guess -- and when defining alternatives one has to exlicitly enable it.  
  
Because the port from Boost.Spirit X3 will be a common use case I would recommend:  
  
* List this in the comparison page between Boost.Spirit and Boost.Parser.  
* Add a section in the documentaiton that:  
  * describes how backtracking works here,  
  * how you migrate from Boost.Spirit,  
  * the difference between `>` and `>>`.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-01-07 19:24:18 UTC  
> Url: https://github.com/boostorg/parser/issues/35#issuecomment-1880148130  

I'm fine describing backtracking more fully (and I'm going to), but I don't understand the statement that X3 does not have backtracking on by default.  Consider the case you reported earlier, slightly modified:  
  
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
  auto parse_x = bp::int_ >> bp::int_;  
  auto parse_y = +parse_x >> bp::int_;  
  
  Y y;  
  auto b = bp::parse("1 3 4", parse_y, bp::ws, y);  
}  
```  
I changed it so that it parses `int`s all the time, instead of a `char` at the beginning of `parse_x`.  
  
This still works because the second iteration of `parse_x`, having eaten `"1 3"` fails to eat two more ints, since it sees only `"4"` next in the input.  `parse_y`, in which `+parse_x` is a subparser, must then backtrack a bit, and parse the `"4"` using the `bp::int_` at the end of `parse_y`.  This is how Spriit 1 and 2 worked.  Is this different from how X3 works?  If so, I can't find that in the X3 docs.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-01-07 20:25:34 UTC  
> Url: https://github.com/boostorg/parser/issues/35#issuecomment-1880161981  

My apologies. I misremembered the situation. Backtracking works in Boost.Spirit, at least in general.  
It is the unintuitive behaviour of the alternative parser that requires the use of `hold` directive. It does not reset attributes from the alternative that didn't ultimately match.  
I can refer you to the short email thread from 13 years ago:|  
https://sourceforge.net/p/spirit/mailman/spirit-general/thread/AANLkTimwgyG85kDLKwpd9uwSkaKw%2BwvMcnU97i9dvA%3De%40mail.gmail.com/#msg25929065

---

## Comment 3

> Username: akrzemi1  
> Created at: 2024-01-07 22:33:54 UTC  
> Updated at: 2024-01-07 22:34:12 UTC  
> Url: https://github.com/boostorg/parser/issues/35#issuecomment-1880200048  

And given that, this PR doesn't make sense. Maybe there is a smaller issue with the alternative, but that would not be this PR.
