# #60 - "optional sequence" attribute should collapse to vector [Closed]

> Username: akrzemi1  
> Created at: 2024-01-12 21:14:20 UTC  
> Updated at: 2024-01-14 02:13:20 UTC  
> Closed at: 2024-01-13 03:55:21 UTC  
> Url: https://github.com/boostorg/parser/issues/60  

another program working in Boost.Spirit but not Boost.Parser. The former allows the attribute of "optional list" (`-(char_ % sep)`) to be converted to `std::string<char>`. One could argue if a parser like this is a good idea, but Boost.Spirit allowed it, and it looks like I decided to use it in my parser, years ago.   
  
```c++  
#ifdef WITH_BOOST_SPIRIT  
#include <boost/spirit/include/qi.hpp>  
namespace bp = boost::spirit::qi;  
#else  
#include <boost/parser/parser.hpp>  
namespace bp = boost::parser;  
#endif  
#include <boost/fusion/adapted/struct/adapt_struct.hpp>  
  
struct X  
{  
  std::vector<char> chs;  
  int i;  
};  
  
BOOST_FUSION_ADAPT_STRUCT(X, chs, i)  
  
int main()  
{  
#ifdef WITH_BOOST_SPIRIT  
  auto alnum = bp::ascii::alnum;  
  auto alpha = bp::alpha;  
#else  
  auto alnum = bp::ascii::alnum;  
  auto alpha = bp::ascii::alpha;  
#endif  
  
  auto list = bp::char_ % ',';  
  auto myParser = (-list) >> bp::int_;  
  
  std::string input = "a 7";  
  X result;  
  
#ifdef WITH_BOOST_SPIRIT  
  auto begin = input.begin();  
  bool b = bp::phrase_parse(begin, input.end(), myParser, bp::ascii::space, result);  
#else  
  auto b = bp::parse(input, myParser, bp::ws, result);  
#endif  
  
  if (b)  
    std::cout << ":-) " << result.i << std::endl;  
  else  
    std::cout << ":-(" << std::endl;  
}  
  
```

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-01-13 03:55:22 UTC  
> Url: https://github.com/boostorg/parser/issues/60#issuecomment-1890291345  

I can see why Spirit allowed this, but this is exactly the kind of thing I'm trying to avoid with the Boost.Parser attribute rules -- it's a special rule that's hard to remember.  i'm not going to copy this one.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-01-13 09:14:56 UTC  
> Url: https://github.com/boostorg/parser/issues/60#issuecomment-1890390671  

Let me challenge your position. My use case is this. I need to parse -- as a sub-parser -- a comma-separated list of zero or more elements (say, `char`s). The "zero or more elements" part is naturally expressed via `vector<T>`, but the `%` combinator does not match to an empty list (neither in Boost.Spirit nor in Boost.Parser, see example below). Maybe it is the deficiency in the `%` combinator?   
  
Anyway, my workaround for the situation was to combine it with the `-` combinator, and Boost.Spirit honored this workaround. Boost.Spirit has a set of rules for merging the attributes that is both consistent, well defined and practical.  
  
You say that Boost.Spirit are hard to remember, but whose problem is it? If I -- the user -- don't remember that the rule "merge `-(p%s)` to `vector<Tp>`" exists , I will just not use it, and will parse into `optional<vector<T>>`. It works in moth libraries (see example 1). But if I need the rule and know it exists, I should be able to use it with no harm to the users that are unaware of the rule.  
  
Interestingly, Boost.Parser appears to have this merge rule for the top level parser (see example 2). It only stops working when we embed it in a higher-level parser, as in the initial example from this PR. So it looks to me Boost.Parser is self-inconsistent here. There are two ways to resolve it: either ban this merge rule consistently, or allow it consistently.  
  
If you choose to ban it consistently, then I request a recommendation how I should address my use case with Boost.Parser?  
  
My use case: parse a zero or more comma-separated elements into a `vector`.   
  
  
Example1. This works both in Boost.Spirit and Boost.Parser:  
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
  
int main()  
{  
#ifdef WITH_BOOST_SPIRIT  
  auto alnum = bp::ascii::alnum;  
  auto alpha = bp::alpha;  
#else  
  auto alnum = bp::ascii::alnum;  
  auto alpha = bp::ascii::alpha;  
#endif  
  
  auto myParser = -(bp::int_ % ',');  
  
  std::string input = "";  
  optional<std::vector<int>> result;  
  
#ifdef WITH_BOOST_SPIRIT  
  auto begin = input.begin();  
  bool b = bp::phrase_parse(begin, input.end(), myParser, bp::ascii::space, result);  
#else  
  auto b = bp::parse(input, myParser, bp::ws, result);  
#endif  
  
  if (b)  
    std::cout << ":-) " << std::endl;  
  else  
    std::cout << ":-(" << std::endl;  
}  
```  
  
Example 2. Works in both libraries  
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
  
int main()  
{  
#ifdef WITH_BOOST_SPIRIT  
  auto alnum = bp::ascii::alnum;  
  auto alpha = bp::alpha;  
#else  
  auto alnum = bp::ascii::alnum;  
  auto alpha = bp::ascii::alpha;  
#endif  
  
  auto myParser = -(bp::int_ % ',');  
  
  std::string input = "1, 2";  
  std::vector<int> result;  
  
#ifdef WITH_BOOST_SPIRIT  
  auto begin = input.begin();  
  bool b = bp::phrase_parse(begin, input.end(), myParser, bp::ascii::space, result);  
#else  
  auto b = bp::parse(input, myParser, bp::ws, result);  
#endif  
  
  if (b)  
    std::cout << ":-) " << result.size() << std::endl;  
  else  
    std::cout << ":-(" << std::endl;  
}  
  
```

---

## Comment 3

> Username: tzlaine  
> Created at: 2024-01-13 21:04:01 UTC  
> Url: https://github.com/boostorg/parser/issues/60#issuecomment-1890768293  

Like I said before, I understand why Spirit works that way -- there's no loss of information, so why not?  However, I don't like that approach.  
  
When I write `operator-`, I get a `std::optional`.  That's a simple rule.  
  
If I *did* write `operator-`, I was opting in to getting a `std::optional`.  The code expresses my intent.  If the library changes my written intent, there better be a damn good reason.  
  
There is an exception to the "simple rule" above -- if I write `operator-` before or after some container it could combine with, it gets slurped up into the container.  I consider this a "damn good reason", because this is a really common use case.  (I'm wrapping up work on `separate[]` that keep the attributes non-combining in these cases.  So `separate[+int_ >> -int_]` would have the attribute `tuple<std::vector<int>, std::optional<int>>`.  That will make opting out of this exception very easy.)  
  
"I wrote `-+int_` but I really want a `std::vector<int>` instead of a `std::optional<std::vector<int>>`" is not a really common use case.  
  
This semantic is more complicated than your simple example above let's on.  Remember, `int_ | eps` and `-int_` are supposed to be semantically equivalent.  I say this over and over in the docs, but even if I hadn't, changing this would be a profound violation of the principle of least surprise.  So, if they're equivalent, I need to apply the same rule to `int_ | eps`.  Also, I probably need to apply it to `if_(cond)[int_]`, which is also a `std::optional<int>`.  This is a lot to remember.  This is complicated to implement and maintain.  
  
I've been using Spirit 1 and later Spirit 2 since they were released.  I did not know about the rule we're discussing.  Having a small set of rules that the user can internalize is vital; if the attribute generated is different from my expressed intent, that's a problem.  For this not to be a problem, I need to be able to understand the rules, so I can express my intent, and not be surprised.  
  
As for how you can adapt your code?  With a rule of course!  
  
```c++  
#include <boost/parser/parser.hpp>  
  
namespace bp = boost::parser;  
  
struct X  
{  
    std::vector<char> chs;  
    int i;  
};  
  
auto push_back = [](auto & ctx) { _val(ctx).push_back(_attr(ctx)); };  
  
bp::rule<struct vec_chars_tag, std::vector<char>> vec_chars = "vec_chars";  
auto vec_chars_def = -(bp::char_[push_back] % ',');  
BOOST_PARSER_DEFINE_RULES(vec_chars);  
  
int main()  
{  
    auto myParser = vec_chars >> bp::int_;  
  
    std::string input = "a 7";  
    X result;  
  
    auto b = bp::parse(input, myParser, bp::ws, result);  
  
    if (b)  
        std::cout << ":-) " << result.i << std::endl;  
    else  
        std::cout << ":-(" << std::endl;  
}  
```  
  
The last issue is that the top-level-parse attribute matching is looser than the down-in-the-details attribute matching.  That's true.  I had a good example of this before, but removed it from the docs when it became well-formed.  Your example will replace it.  
  
In short, I think this is a great example for the docs, but I still am unwilling to make the change.

---

## Comment 4

> Username: akrzemi1  
> Created at: 2024-01-13 21:37:48 UTC  
> Url: https://github.com/boostorg/parser/issues/60#issuecomment-1890774978  

I am not sure if I am using the correct terms here. A (combined) parser has its attribute type based on well-defined rules. For `-(int_ % ',')` that would be `optional<vector<int>>`. That's fine. But in addition to that, when I pass `vector<int>` to function `parse()` could you convert the value from `optional<vector<int>>` to `vector<int>`? Maybe this is a different guestion than changing the parser attribute type? Boost.Spirit must do it this way, given that it allows me to pass both `optional<vector<int>>` and `vector<int>`.  
  
Second, maybe this case warrants a dedicated parser? Boost.Spirit decided to add a non-canonical `%` operator for the common case. But is "separated list of zero or more elements" not common also?

---

## Comment 5

> Username: tzlaine  
> Created at: 2024-01-13 22:12:52 UTC  
> Url: https://github.com/boostorg/parser/issues/60#issuecomment-1890781066  

Well, I just know that the top-level parse takes a `T` out-param, and your parser `p` produces an attribute `ATTR(p)`, and if `decltype(x = std::declval<ATTR(p)>())` is well-formed, the top-level parse is well-formed.  Spirit may be doing more than that, like making sure that an `optional<T>` assigns to a `T`, but I'm not sure.  
  
I realized after writing the rule-based example that if you write it out, you also get a `std::vector` attribute:  
  
```c++  
auto parser = -int_ >> *(',' >> int_);  
```  
  
I don't think there's need for a custom operator.

---

## Comment 6

> Username: akrzemi1  
> Created at: 2024-01-14 00:02:18 UTC  
> Updated at: 2024-01-14 00:03:56 UTC  
> Url: https://github.com/boostorg/parser/issues/60#issuecomment-1890799529  

Parsing with `(int_ % ',') >> char_` into `tuple<vector<int>, char>` compiles in both. (example 1)  
  
Parsing with `int_ >> *(',' >> int_)) >> char_` into `tuple<vector<int>, char>` compiles with Boost.Parse, but not in Boost.Spirit. (example 2)  
  
Interestingly, parsing with  `int_ >> *(',' >> int_)) >> char_`  into `vector<int>` fails in Boost.Parse but **compiles** in Boost.Spirit. (example 3)  
  
Now I start to appreciate the stricter rules.  
  
So, your workaround will work for my case, for no obvious reasons, but I still have a design remark:  
  
 * `-int_ >> *(',' >> int_)`  
 * `-(int_ % ',')`  
  
These two parsers are equivalent in terms of what they consider valid/invalid input, but they have different attributes. Does this not violate some consistency rules?  
  
Also, it now looks like Boost.Parse has one set of rules for attributes and Boost.Spirit has another. Maybe it is worth documenting these differences.  
  
Example 1  
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
  
struct X  
{  
  std::vector<int> vec;  
  char val;  
};  
  
BOOST_FUSION_ADAPT_STRUCT(X, vec, val)  
  
int main()  
{  
#ifdef WITH_BOOST_SPIRIT  
  auto alnum = bp::ascii::alnum;  
  auto alpha = bp::alpha;  
#else  
  auto alnum = bp::ascii::alnum;  
  auto alpha = bp::ascii::alpha;  
#endif  
  
  auto myParser = (bp::int_ % ',') >> bp::char_;  
  
  std::string input = "1, 2 c";  
  X result;  
  
#ifdef WITH_BOOST_SPIRIT  
  auto begin = input.begin();  
  bool b = bp::phrase_parse(begin, input.end(), myParser, bp::ascii::space, result);  
#else  
  auto b = bp::parse(input, myParser, bp::ws, result);  
#endif  
  
  if (b)  
    std::cout << ":-) " << std::endl;  
  else  
    std::cout << ":-(" << std::endl;  
}  
  
```  
  
Example 2  
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
  
struct X  
{  
  std::vector<int> vec;  
  char val;  
};  
  
BOOST_FUSION_ADAPT_STRUCT(X, vec, val)  
  
int main()  
{  
#ifdef WITH_BOOST_SPIRIT  
  auto alnum = bp::ascii::alnum;  
  auto alpha = bp::alpha;  
#else  
  auto alnum = bp::ascii::alnum;  
  auto alpha = bp::ascii::alpha;  
#endif  
  
  auto myParser = (bp::int_ >> *(',' >> bp::int_)) >> bp::char_;  
  
  std::string input = "1, 2 c";  
  X result;  
  
#ifdef WITH_BOOST_SPIRIT  
  auto begin = input.begin();  
  bool b = bp::phrase_parse(begin, input.end(), myParser, bp::ascii::space, result);  
#else  
  auto b = bp::parse(input, myParser, bp::ws, result);  
#endif  
  
  if (b)  
    std::cout << ":-) " << 1 << std::endl;  
  else  
    std::cout << ":-(" << std::endl;  
}  
```  
  
Example 3  
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
  
struct X  
{  
  std::vector<int> vec;  
  char val;  
};  
  
BOOST_FUSION_ADAPT_STRUCT(X, vec, val)  
  
int main()  
{  
#ifdef WITH_BOOST_SPIRIT  
  auto alnum = bp::ascii::alnum;  
  auto alpha = bp::alpha;  
#else  
  auto alnum = bp::ascii::alnum;  
  auto alpha = bp::ascii::alpha;  
#endif  
  
  auto myParser = (bp::int_ >> *(',' >> bp::int_)) >> bp::char_;  
    
  std::string input = "1, 2 c";  
  std::vector<int> result;  
  
#ifdef WITH_BOOST_SPIRIT  
  auto begin = input.begin();  
  bool b = bp::phrase_parse(begin, input.end(), myParser, bp::ascii::space, result);  
#else  
  auto b = bp::parse(input, myParser, bp::ws, result);  
#endif  
  
  if (b)  
    std::cout << ":-) " << 1 << std::endl;  
  else  
    std::cout << ":-(" << std::endl;  
}  
  
```

---

## Comment 7

> Username: tzlaine  
> Created at: 2024-01-14 02:13:19 UTC  
> Url: https://github.com/boostorg/parser/issues/60#issuecomment-1890824131  

> Parsing with `(int_ % ',') >> char_` into `tuple<vector<int>, char>` compiles in both. (example 1)  
>   
> Parsing with `int_ >> *(',' >> int_)) >> char_` into `tuple<vector<int>, char>` compiles with Boost.Parse, but not in Boost.Spirit. (example 2)  
>   
> Interestingly, parsing with `int_ >> *(',' >> int_)) >> char_` into `vector<int>` fails in Boost.Parse but **compiles** in Boost.Spirit. (example 3)  
>   
> Now I start to appreciate the stricter rules.  
  
:)  
  
> So, your workaround will work for my case, for no obvious reasons, but I still have a design remark:  
>   
> * `-int_ >> *(',' >> int_)`  
> * `-(int_ % ',')`  
>   
> These two parsers are equivalent in terms of what they consider valid/invalid input, but they have different attributes. Does this not violate some consistency rules?  
  
I don't think so.  These should be equivalent:  
  
* `-(int_ % ',')`  
* `-(int_ >> *(',' >> int_))`  
  
... and they are.  However, `-int_ >> *(',' >> int_)` is a different expression, with different semantics, and so it has a difference attribute.  Now that I look at it, it accepts `", 42"`, which is probably not what you want.  Another iteration that should fit your use case better is `int_ % ',' | attr(std::vector<int>{})`.  This is also a distinct expression, though with the same attribute type as `-int_ >> *(',' >> int_)`.  
  
> Also, it now looks like Boost.Parse has one set of rules for attributes and Boost.Spirit has another. Maybe it is worth documenting these differences.  
  
About that.  One of the motivating factors for my writing Boost.Parser is that I never understood all the attribute matching rules, and they're not all documented.
