# #177 fixes problem with move_to [Merged]

> Username: hia3  
> Created at: 2016-03-19 05:31:46 UTC  
> Updated at: 2016-03-19 21:11:51 UTC  
> Merged at: 2016-03-19 09:30:51 UTC  
> Closed at: 2016-03-19 09:30:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/177  

Fix bug described here - http://article.gmane.org/gmane.comp.lib.boost.devel/266211

---

## Comment 1

> Username: djowel  
> Created_at: 2016-03-19 05:34:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/177#issuecomment-198638447  

Please explain the "problem with move_to" and how this fixes it. I'll also need some tests the exhibit the problem you mention.

---

## Comment 2

> Username: hia3  
> Created_at: 2016-03-19 05:54:10 UTC  
> Updated_at: 2016-03-19 05:55:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/177#issuecomment-198641024  

Should I explain it here in comments or in commit description? Should I add example from http://article.gmane.org/gmane.comp.lib.boost.devel/266211 to the commit?

---

## Comment 3

> Username: hia3  
> Created_at: 2016-03-19 06:05:27 UTC  
> Updated_at: 2016-03-19 06:22:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/177#issuecomment-198642134  

Problem was that it was trying to call this traits::move_to https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/x3/support/traits/move_to.hpp#L171 for (in my case) Source = a1 and Dest = a1.  
  
Message from compiler:  
/..../spirit-develop/include/boost/spirit/home/x3/support/traits/move_to.hpp:172:17: note: in instantiation of function template specialization 'boost::spirit::x3::traits::detail::move_to<ast::a1, ast::a1>' requested here  
  
Patch just adds all the forward declarations of traits::move_to so that they are visible from detail namespace.

---

## Comment 4

> Username: hia3  
> Created_at: 2016-03-19 06:21:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/177#issuecomment-198646255  

I'm not sure all those forwarding references and `std::move`s are necessary. Because 1) it can be `std::move`d when real work is done (in assignment for example) and 2) `default_transform_attribute::post` gets `attr` by reference anyway.

---

## Comment 5

> Username: djowel  
> Created_at: 2016-03-19 06:44:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/177#issuecomment-198648634  

move_to in develop is work in progress. i'd be more comfortable if you can just provide a minimal test case that exhibits the problem you mention.

---

## Comment 6

> Username: hia3  
> Created_at: 2016-03-19 07:00:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/177#issuecomment-198649375  

Isn't it minimal enough?  
  
```  
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
  
auto a0 = rule<class i0, std::string>{} = lexeme[+alpha];  
auto a1 = rule<class i1, ast::a1>{} = a0;  
  
auto a2_v2 = rule<class a2_v2, std::vector<double>>{} = -(double_ % ',');  
auto a2 = rule<class i2, ast::a2>{} = a1 >> '(' >> a2_v2 >> ')';  
  
auto a3_v1 = rule<class a3_v1, std::vector<ast::a2>>{} = *(a2 >> ';');  
auto a3 = rule<class i3, ast::a3>{} = a3_v1;  
  
using data = ast::a3;  
  
int main()  
{  
    std::string script = "";  
  
    auto begin = script.begin();  
    auto   end = script.end();  
  
    data v;  
  
    bool ok = (boost::spirit::x3::phrase_parse(begin, end, a3, space, v) && begin == end);  
}  
```

---

## Comment 7

> Username: djowel  
> Created_at: 2016-03-19 07:00:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/177#issuecomment-198649390  

perfect!

---

## Comment 8

> Username: djowel  
> Created_at: 2016-03-19 09:29:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/177#issuecomment-198673899  

Ok, I'm going to merge this patch. Seems it's not in conflict with the work I am doing.

---
