# #215 - Incorrect attribute with * and + [Closed]

> Username: dunkyp  
> Created at: 2025-02-24 11:03:36 UTC  
> Updated at: 2025-03-01 22:36:11 UTC  
> Closed at: 2025-02-24 23:53:39 UTC  
> Url: https://github.com/boostorg/parser/issues/215  

I'm seeing a weird (off by one) error in a simple parser:  
  
```  
#define BOOST_PARSER_DISABLE_CONCEPTS  
#include <boost/parser/parser.hpp>  
#include <print>  
  
  
namespace bp = boost::parser;  
int main() {  
  const auto id_set_action = [](auto &ctx) {  
    const auto& [left, right] = _attr(ctx);  
    std::println("{} = {}", left, right);  
  };  
  
  const auto id_parser = bp::char_('a', 'z') > *bp::char_('a', 'z');  
  
  const auto id_set = (id_parser >> '=' >> id_parser)[id_set_action];  
  bp::parse("left=right", id_set);  
  return 0;  
}  
```  
  
I'd expect the output of this program to be `left = right` however it is `leftr = ight` this is the case on both the `master` and `develop` branches.

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-02-24 23:53:39 UTC  
> Url: https://github.com/boostorg/parser/issues/215#issuecomment-2679968076  

This is not a bug.  It's an unfortunate consequence of the rules that I picked for how attributes are generated from sequence parsers.  In this section of the docs:  
  
https://www.boost.org/doc/libs/1_87_0/doc/html/boost_parser/tutorial.html#boost_parser.tutorial.attribute_generation.sequence_parser_attribute_rules  
  
I describe the very simple rules for attributes of sequence parsers.  I chose these rules in part for their simplicity -- I can describe how they work in a few bullet points, and you can internalize them due to their simplicity.  This was a reaction to the Boost.Spirit attribute generation rules, which are so complicated that I was never able to fully internalize them, even after decades of using them.  
  
Ok, on to your example.  
  
When you write this pair of lines:  
```c++  
const auto id_parser = bp::char_('a', 'z') > *bp::char_('a', 'z');  
  
const auto id_set = (id_parser >> '=' >> id_parser)[id_set_action];  
```  
  
It seems as if `id_parser` exists as a distinct unit of work in your parser, but it does not.  Those two lines are equivalent to this one line:  
  
```c++  
const auto id_set = (  
  bp::char_('a', 'z') > *bp::char_('a', 'z') >>  
  '=' >>  
  bp::char_('a', 'z') > *bp::char_('a', 'z')  
)[id_set_action];  
```  
  
If you refer back to the rules, it says that: 1) non-attribute parsers are ignored; and 2) attributes T and U are combined into a single sequence attributes if T is a sequence of U's, or U is a sequence of T's.  
  
Here is is again, with the parsers annotated with names:  
  
```c++  
const auto id_set = (  
  /*A*/ bp::char_('a', 'z') > /*B*/ *bp::char_('a', 'z') >>  
  /*C*/ '=' >>  
  /*D*/ bp::char_('a', 'z') > /*E*/ *bp::char_('a', 'z')  
)[id_set_action];  
```  
  
So, the combining steps are: A and B get merged into a string, then C is ignored, and then D gets merged into the string formed earlier by A+B.  Then, we have E.  E does not combine with D, as D was already consumed.  
  
The "fix" for this -- that is, the way to get the behavior you expect -- is to create a parser that does not combine with other parsers around it.  To do that, you use a rule.  A rule is a bona fide unit of work, and is treated as distinct from other parsers it is used with.  
  
```c++  
namespace bp = boost::parser;  
bp::rule<struct id_parser_tag, std::string> id_parser = "ID parser";  
auto const id_parser_def = bp::char_('a', 'z') > *bp::char_('a', 'z');  
BOOST_PARSER_DEFINE_RULES(id_parser);  
```  
  
Then, later, use it just as you used the previous non-rule version:  
  
```c++  
const auto id_set = (id_parser >> '=' >> id_parser)[id_set_action];  
```  
  
This produces the results you expect, since only the `bp::char_('a', 'z') > *bp::char_('a', 'z')` parser is ever eligible for combining.  
  
I know this may seem unsatisfying.  One possible way to address all of this would be to add more rules to the attribute combination logic, like "don't combine across the presence of an ignored attribute" ("C" in the example above).  This is the approach that Spirit took, and as I mentioned I find that even less satisfying.

---

## Comment 2

> Username: dunkyp  
> Created at: 2025-02-27 09:01:13 UTC  
> Url: https://github.com/boostorg/parser/issues/215#issuecomment-2687315060  

Thanks, that's a really helpful and detailed description!

---

## Comment 3

> Username: tzlaine  
> Created at: 2025-03-01 22:36:10 UTC  
> Url: https://github.com/boostorg/parser/issues/215#issuecomment-2692451496  

Absolutely!  I wrote it up originally with the thought that other would find it here.  Then I realized this is a doc failure.  So, I added it to the docs, used your example, and thanked you for the example in the docs:  
  
78bc141d5f8859c3a4a51f1e0291898b835fc676
