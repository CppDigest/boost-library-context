# #551 - With dynamic grammar skipper doesn't work [Closed]

> Username: allopislozano  
> Created at: 2019-11-15 16:45:17 UTC  
> Updated at: 2021-06-01 20:23:39 UTC  
> Closed at: 2021-06-01 20:23:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/551  

I think this is a bug. I create dynamically a x3::any_parser  that can parse   strings like "t1"  "t2" etc  and when I compose it, it doesn't parse correctly unless I use a x3::lexeme[] around it. Looks like the skipper is not working properly in that case or something...  
  
```  
#include<vector>  
#include<string>  
#include <utility>  
#include <iostream>  
#define BOOST_SPIRIT_X3_DEBUG  
#include <boost/spirit/home/x3.hpp>  
using std::vector;  
using std::string;  
  namespace x3 = boost::spirit::x3;  
  
x3::any_parser<string::iterator> makeComposedParser(vector<string> const & ss)  
{  
      
    x3::any_parser<string::iterator> p= x3::eps(false); //placeholder  
    for (auto && s : ss)  
        p = p | x3::lit(s) >> x3::int_;  
     return p;  
}  
  
int main()  
{  
    vector<string> parserMustDetect = {"t", "b"};  
    auto a = makeComposedParser(parserMustDetect);  
      
    vector<string> toParse = {"t1","t2"};  
    for (auto && s : toParse){  
        string x;  
        auto b = s.begin();  
        auto e = s.end();  
        std::cout << "parsed?" << x3::parse(b,e,a)<< "\n";  
    }  
    string x = "X t1";  
    auto b = x.begin();  
    auto e = x.end();  
  
    //Should parse but it doesnt  
    std::cout << "parsed ? " << x3::phrase_parse(b,e,x3::lit("X") >> a, x3::space) << "\n";  
    //Lexeme helps  
    std::cout << "parsed ? " << x3::phrase_parse(b,e,x3::lit("X") >> x3::lexeme[a], x3::space);  
  
}  
 ```

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-11-15 17:13:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/551#issuecomment-554447093  

Unfortunately it is by design.  
  
Sequence parser does not do skipping by itself, it relies on a fact that underlying parser will do skipping. The `a` parser does not do skipping because `any_parser` with default context template argument drops skipper, that's why in the first case no skipping happens, and in the second case `lexeme` does skipping.

---

## Comment 2

> Username: allopislozano  
> Created at: 2019-11-18 09:18:16 UTC  
> Url: https://github.com/boostorg/spirit/issues/551#issuecomment-554926432  

So is this considered a bug ? Or is it a limitation that is not possible to solve?  In that case I think it would be nice to document that limitation. In fact any_parser is undocumented AFAIK

---

## Comment 3

> Username: Kojoley  
> Created at: 2019-11-18 13:45:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/551#issuecomment-555021282  

It depends on a perspective. I see it as an unintuitive behavior that I would like to fix but: 1) it is forged into `rule` too (https://wandbox.org/permlink/QtXPYSDTJEqtHqre), fixing only `any_parser` will just bring inconsistency 2) it requires non-trivial changes to deal with non-preskipping parsers (like `no_case`, `attr`, etc.) 3) it will break backward compatibility.  
  
@djowel do you have anything to say?

---

## Comment 4

> Username: Kojoley  
> Created at: 2021-06-01 20:23:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/551#issuecomment-852421581  

The only thing that could be done is to add a static assertion, but I think it will be too annoying to wrap a rule with `no_skip[a]`.  
  
Probably I was not clear enough in my first response, the example code could be fixed by replacing `x3::any_parser<string::iterator>` with `x3::any_parser<string::iterator, x3::unused_type, x3::phrase_parse_context<x3::space_type>::type>` or `p = p | x3::lit(s) >> x3::int_` `p = p | x3::skip(x3::space)[x3::lit(s) >> x3::int_]`.
