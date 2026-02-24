# #178 X3: Kleene star can't into single-element sequence [Closed]

> Username: hia3  
> Created at: 2016-03-19 21:08:56 UTC  
> Updated at: 2025-05-09 14:45:02 UTC  
> Closed at: 2025-05-09 14:44:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/178  

This will fail to compile with error "'value_type': is not a member of 'ast::a3'".  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
#include <boost/fusion/include/define_struct_inline.hpp>  
  
#include <string>  
#include <vector>  
  
namespace ast  
{  
    BOOST_FUSION_DEFINE_STRUCT_INLINE  
    (  
        a1,  
        (std::string, v)  
    )  
  
    BOOST_FUSION_DEFINE_STRUCT_INLINE  
    (  
        a2,  
        (a1, v1)  
        (std::vector<double>, v2)  
    )  
  
    BOOST_FUSION_DEFINE_STRUCT_INLINE  
    (  
        a3,  
        (std::vector<a2>, v1)  
    )  
}  
  
using namespace boost::spirit::x3;  
  
auto a1 = rule<class i1, ast::a1>{} = lexeme[+alpha];  
  
auto a2 = rule<class i2, ast::a2>{} = a1 >> '(' >> -(double_ % ',') >> ')';  
  
auto a3 = rule<class i3, ast::a3>{} = *(a2 >> ';');  
  
using data = ast::a3;  
  
int main()  
{  
    std::string script = "foo(1,2,3);";  
  
    auto begin = script.begin();  
    auto   end = script.end();  
  
    data v;  
  
    bool ok = (boost::spirit::x3::phrase_parse(begin, end, a3, space, v) && begin == end);  
}  
```  
  
Fixed by adding container_value specialization: for single-element fusion sequence return container_value of that one element.  
  
---  
EDIT by maintainer: fixed styling issues

---

## Comment 1

> Username: hia3  
> Created_at: 2016-03-19 21:25:23 UTC  
> Url: https://github.com/boostorg/spirit/pull/178#issuecomment-198787215  

Btw, adding #define BOOST_SPIRIT_X3_DEBUG will break this sample program again.

---

## Comment 2

> Username: djowel  
> Created_at: 2016-03-19 22:44:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/178#issuecomment-198802189  

I'm not sure about this one. Why do you have to wrap the container into a fusion vector? Why not simply use std::vector<double> and std::vectorstd::vector<double> ?

---

## Comment 3

> Username: hia3  
> Created_at: 2016-03-19 23:17:23 UTC  
> Url: https://github.com/boostorg/spirit/pull/178#issuecomment-198805263  

Why not?  
  
I'm not saying "you have to". This might be the result of simplification of some existing code. Do you want to not to compile simpler version of the code? Then `static_assert` would help a lot. Or this might be the 1st parser someone new to the library (like myself) trying to write.  
  
If I'd adapting the existring struct, I could write `using value_type = whatever;`, but with `BOOST_FUSION_DEFINE_STRUCT_INLINE` there is no way I can do it.  
  
Why `variable` here https://github.com/boostorg/spirit/blob/develop/example/x3/calc/calc9/ast.hpp#L27 is not a string?

---

## Comment 4

> Username: hia3  
> Created_at: 2016-03-19 23:43:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/178#issuecomment-198809415  

Btw, `ast::a3` is unchanged from my previous pull request (it was single element seqence too). Only thing changed is a3 rule from this:  
  
```  
auto a3_v1 = rule<class a3_v1, std::vector<ast::a2>>{} = *(a2 >> ';');  
auto a3    = rule<class i3, ast::a3>{}                 = a3_v1;  
```  
  
to this  
  
```  
auto a3    = rule<class i3, ast::a3>{}                 = *(a2 >> ';');  
```  
  
And now it suddenly requires `value_type` to be defined.

---

## Comment 5

> Username: djowel  
> Created_at: 2016-03-19 23:59:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/178#issuecomment-198810710  

I'll have to think about this some more as it introduces a new behavior. The previous behavior in which you provided a patch for is suspicious to begin with. What we should probably do is to distill the test code to the bare essentials so I (we) can analyze and reason out with the correct behavior. I fear that we are opening a can of worms if we allow single-element-tuple-with-container == container.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2018-09-21 13:05:41 UTC  
> Updated_at: 2018-09-25 00:39:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/178#issuecomment-423524163  

MWE:  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/include/define_struct_inline.hpp>  
#include <string>  
  
namespace ast  
{  
BOOST_FUSION_DEFINE_STRUCT_INLINE  
(  
    a3,  
    (std::vector<int>, v1)  
)  
}  
  
int main()  
{  
    namespace x3 = boost::spirit::x3;  
  
    auto test = [](std::string s, auto&& p)  
    {  
        ast::a3 v;  
  
        auto it = s.begin();  
        return (x3::phrase_parse(it, s.end(), p, x3::space, v) && it == s.end());  
    };  
  
    test("1 2 3 ", *x3::int_);          // OK  
    test("1;2;3 ",  x3::int_ % ';');    // OK  
    test("1;2;3;", *(x3::int_ >> ';')); // Fail to compile  
}  
```

---

## Comment 7

> Username: saki7  
> Created_at: 2025-05-09 14:44:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/178#issuecomment-2866830640  

The code shown above seems to work in 2025. We already have tests that consist of same combination of grammars: e.g. https://github.com/boostorg/spirit/blob/328d940c372367a8d76a48ed956e870b56738ce9/test/x3/expect.ipp#L749

---
