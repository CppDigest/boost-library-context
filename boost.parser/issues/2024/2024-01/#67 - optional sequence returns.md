# #67 - optional sequence returns [Closed]

> Username: akrzemi1  
> Created at: 2024-01-15 20:30:50 UTC  
> Updated at: 2024-01-21 20:06:48 UTC  
> Closed at: 2024-01-15 22:30:31 UTC  
> Url: https://github.com/boostorg/parser/issues/67  

Going back to the zero-or-more-element separated sequence, the workaround with `attr` proposed in https://github.com/tzlaine/parser/issues/60 doesn't work in more complex cases. The following, a bit unfair, example compares the Boost.Spirit solution with `-(p % s)` and Boost.Parser solution with `(p % s) | attr(A{})`, wehre `p` parses a tuple.  
  
The problem is that when I use my aggregate type `A`, then attribute `variant<tuple<M1, M2>, A>` is not merged to just `A` (or `tuple<M1, M2>`).  In other words, `A` and `tuple<M1, M2>` are not treated as "same" or "equivalent".  
  
Maybe this could be changed? If not, I ask you to reconsider https://github.com/tzlaine/parser/issues/63. Or maybe there is a way to define one's own directive? I find the use of a rule -- involving the use of macros and multiple definitions -- too big a solution for so common a problem.  
  
  
```c++  
#ifdef WITH_BOOST_SPIRIT  
#include <boost/spirit/include/qi.hpp>  
namespace bp = boost::spirit::qi;  
using boost::optional;  
#else  
#include <boost/parser/parser.hpp>  
namespace bp = boost::parser;  
using std::optional;  
#endif  
#include <boost/fusion/adapted/struct/adapt_struct.hpp>  
  
struct P  
{  
  char c;  
  int i;  
};  
BOOST_FUSION_ADAPT_STRUCT(P, c, i);  
  
int main()  
{  
#ifdef WITH_BOOST_SPIRIT  
  auto alnum = bp::ascii::alnum;  
  auto alpha = bp::alpha;  
#else  
  auto alnum = bp::ascii::alnum;  
  auto alpha = bp::ascii::alpha;  
#endif  
  
#ifdef WITH_BOOST_SPIRIT  
  auto myParser = -((bp::char_ >> bp::int_) % ',');  
#else  
  auto myParser = ((bp::char_ >> bp::int_) % ',') | bp::attr(std::vector<P>{});  
#endif  
  
  std::string input = "1, 2";  
  std::vector<int> result;  
  
#ifdef WITH_BOOST_SPIRIT  
  auto begin = input.begin();  
  bool b = bp::phrase_parse(begin, input.end(), myParser, bp::ascii::space, result);  
#else  
  auto b = bp::parse(input, myParser, bp::ws, result);  
#endif  
  
  if (b)  
    std::cout << ":-) " << std::endl;  
  else  
    std::cout << ":-( " << std::endl;  
}  
  
```

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-01-15 22:30:31 UTC  
> Url: https://github.com/boostorg/parser/issues/67#issuecomment-1892831982  

Yeah, that doesn't work, but again, that's what rules are for.  You can create a rule like:  
  
```c++  
bp::rule<struct ps_tag, std::vector<P>> ps = "ps";  
auto ps_def = (bp::char_ >> bp::int_) % ',') | bp::eps;  
```  
  
And that will work fine I think.  Rules exist explicitly for this purpose -- to fix the attribute type to something other than the default.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-01-16 09:38:04 UTC  
> Updated at: 2024-01-16 09:39:35 UTC  
> Url: https://github.com/boostorg/parser/issues/67#issuecomment-1893380512  

According to the docs, that would actually be three lines:  
  
```c++  
bp::rule<struct ps_tag, std::vector<P>> ps = "ps";  
auto ps_def = ((bp::char_ >> bp::int_) % ',') | bp::eps;  
BOOST_PARSER_DEFINE_RULE(ps);  
```  
  
And I find it quite problematic. My whole parser looks like this:  
https://github.com/akrzemi1/wdungeon/blob/master/parser.cpp#L33  
It is 15 lines, each defining a parser. I have no recursion. This layout has a nice property -- this is the huge advantage of Boost.Spirit -- that the code looks almost as if I were defining the EBNF description.  
  
This will now break when I need to inject the rule definitions. And I have two of them.   
  
BTW, is there a way to define a *generic* rule? That is, can I specify a rule that applies my pattern (parse `p` zero or more times, comma separated) but allow me to instantiate it for different parsers `p`?

---

## Comment 3

> Username: tzlaine  
> Created at: 2024-01-17 00:09:03 UTC  
> Url: https://github.com/boostorg/parser/issues/67#issuecomment-1894716658  

> According to the docs, that would actually be three lines:  
>   
> ```c++  
> bp::rule<struct ps_tag, std::vector<P>> ps = "ps";  
> auto ps_def = ((bp::char_ >> bp::int_) % ',') | bp::eps;  
> BOOST_PARSER_DEFINE_RULE(ps);  
> ```  
>   
> And I find it quite problematic. My whole parser looks like this: https://github.com/akrzemi1/wdungeon/blob/master/parser.cpp#L33 It is 15 lines, each defining a parser. I have no recursion. This layout has a nice property -- this is the huge advantage of Boost.Spirit -- that the code looks almost as if I were defining the EBNF description.  
>   
> This will now break when I need to inject the rule definitions. And I have two of them.  
  
I told you earlier the problems I have with the attribute combination logic in Spirit.  I don't really think making your example stick to 15 lines instead of 20 is a very strong counter-argument.  Also, the Boost.Parser version could lose 29 lines of ADAPTed_STRUCTs.  
  
> BTW, is there a way to define a _generic_ rule? That is, can I specify a rule that applies my pattern (parse `p` zero or more times, comma separated) but allow me to instantiate it for different parsers `p`?  
  
Well, a rule is just a variable, and you can make variable templates.  The fact that the tag type needs to depend on the template parameter is a small wrinkle, but something like this:  
  
```c++  
template<typename P>  
struct my_rule_tag {};  
  
template<typename P, typename Attr>  
bp::rule<my_rule_tag<P>, std::vector<Attr>> my_rule = "my_rule";  
template<typename P, typename Attr>  
constexpr auto my_rule_def = (P{} % ',') | bp::attr(std::vector<Attr>{});  
```  
  
... might work, but you'll have to write out the function overloads that would normally be generated by `BOOST_PARSER_DEFINE_RULES`; they'll have to gain a template parameter `T`.  You could also just make a non-rule variable template for parsers that already have the right attribute, like:  
  
```c++  
template<typename P>  
constexpr auto no_attr_repeated_p = omit[*p];  
```

---

## Comment 4

> Username: akrzemi1  
> Created at: 2024-01-17 15:19:13 UTC  
> Url: https://github.com/boostorg/parser/issues/67#issuecomment-1896029086  

I acknowledge your goal of having simple attribute merge rules. I am therefore exploring other options. Parametrized rule may not be possible. But a directive looks like something that is already parametrized by the parser.  
  
Is it possible to provide a tool for defining custom directives?

---

## Comment 5

> Username: tzlaine  
> Created at: 2024-01-18 00:37:03 UTC  
> Url: https://github.com/boostorg/parser/issues/67#issuecomment-1897558471  

Maybe?  This is just not something that comes up very often.  In fact, I've never needed such functionality in years and years of using Spirit to write dozens of parsers.  However, I think if I had a compelling use case, I could be persuaded.

---

## Comment 6

> Username: akrzemi1  
> Created at: 2024-01-21 02:14:40 UTC  
> Url: https://github.com/boostorg/parser/issues/67#issuecomment-1902479748  

Ok, so the regression of Boost.Parser relative to Boost.Spirit is that for the parser that needs to parse "zero-or-more occurrences of comma-separated items", I no longer can define it at function or block scope. The only way for me is to use a rule, but a rule can only be defined at namespace scope.  
  
In my program, as long as I were using Boost.Spirit, i was able to encapsulate my parser in a single function body.   
https://github.com/akrzemi1/wdungeon/blob/master/parser.cpp#L33  
Now I will have to move my code outsde the function body. Is that correct?

---

## Comment 7

> Username: tzlaine  
> Created at: 2024-01-21 08:02:56 UTC  
> Url: https://github.com/boostorg/parser/issues/67#issuecomment-1902547505  

Yes, that's correct, except for the characterization of "regression".  It's more like choosing a slightly different set of trade-offs.  
  
Some things to note, though.  Rules and parsers are constant data.  Are you really that sensitive to where you define your constants?  I tend to have lots of namespace-scope contants, and it never caused me any aggravation.  I care about where code is when it affects my ability to do local reasoning.  With code that describes constants... not so much.  
  
Also, so far you've been treating rules as this weird thing that you have to put up with.  Most people who use Spirit (and later Parser) have, and will continue to, use rules all the time.  I encourage you to embrace them as well.  Except for the smallest parsers, rules are essential for organizing things, and writing good tests in particular.

---

## Comment 8

> Username: akrzemi1  
> Created at: 2024-01-21 20:06:47 UTC  
> Url: https://github.com/boostorg/parser/issues/67#issuecomment-1902746692  

My reservation is not towards rules in general, but towards their interface in Boost.Parser. I will describe it in a separate issue.
