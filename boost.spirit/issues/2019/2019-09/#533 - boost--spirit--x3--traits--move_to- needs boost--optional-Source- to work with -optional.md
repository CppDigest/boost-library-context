# #533 - boost::spirit::x3::traits::move_to: needs boost::optional<Source> to work with -optional [Closed]

> Username: duzy  
> Created at: 2019-09-09 14:10:41 UTC  
> Updated at: 2020-06-09 01:15:58 UTC  
> Closed at: 2020-06-08 18:48:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/533  

For a `def` like (https://github.com/extbit/mold/blob/aa0fe70e75d2dfb56915b8307728448d376660a9/include/mold/domain/tildache/parser_def.hpp#L151):  
```c++  
    auto const tild_see_section_def =  
      tild_see_case  
      >> +tild_expr_case  
      >> -tild_else_case  
      >> omit[sk[lit("{{") >> '~' >> "end" >> -lit("see") >> "}}"]]  
      ;  
```  
Where my ast is `tild_else_case = boost::optional<node_list>`, the `taits::move_to` is not working:  
```c++  
    template <typename Source, typename Dest>  
    inline void move_to(Source&& src, Dest& dest)  
    {  
        detail::move_to(std::move(src), dest  
          , typename attribute_category<Dest>::type());  
    }  
```  
  
Reason: when `Source` is boost::optional, `detail::move_to` has no viable working version. (In my case `node_list` is a `container_attribute`.)  
  
This specialization fixes the issue for me (https://github.com/extbit/mold/blob/aa0fe70e75d2dfb56915b8307728448d376660a9/include/mold/domain/tildache/ast.hpp#L12):  
  
```c++  
namespace boost { namespace spirit { namespace x3 { namespace traits  
{  
  template <typename Source, typename Dest>  
  inline void move_to(boost::optional<Source>&& src, Dest& dest)  
  {  
    // For tild_else_case = boost::optional<node_list>  
    if (src) move_to(*src, dest);  
  }  
}}}} // boost::spirit::x3::traits  
```  
  
But a better fix should be added to `boost/spirit/home/x3/support/traits/move_to.hpp`!

---

## Comment 1

> Username: djowel  
> Created at: 2019-09-10 22:29:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/533#issuecomment-530145146  

Could you do a PR for this, including a test that exhibits the issue?

---

## Comment 2

> Username: duzy  
> Created at: 2019-09-11 01:24:56 UTC  
> Updated at: 2019-09-11 01:44:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/533#issuecomment-530180377  

Hi @djowel , I think this trivial test should reveal the issue:  
```c++  
// This test exhibits the issue of parsing from(to?) boost::optional<ast::foo> node.  
// It works well in previous version of spirit::x3.  
//  
// Duzy Chan, 2019-09-11, ExtBit Limited  
  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
  
namespace extbit  
{  
  namespace ast  
  {  
    struct bar  
    {  
      int baz1;  
      int baz2;  
    };  
  
    using foo = boost::optional<bar>;  
  
    struct top  
    {  
      bar a;  
      foo b;  
      bar c;  
    };  
  }  
}  
  
BOOST_FUSION_ADAPT_STRUCT(extbit::ast::top, a, b, c)  
BOOST_FUSION_ADAPT_STRUCT(extbit::ast::bar, baz1, baz2)  
  
namespace extbit  
{  
  namespace parser  
  {  
    namespace x3 = boost::spirit::x3;  
  
    const x3::rule<class top, ast::top> top = "top";  
    const x3::rule<class foo, ast::foo> foo = "foo";  
    using x3::int_;  
  
    auto const top_def = foo >> -foo >> foo;  
    auto const foo_def = int_ >> int_;  
  
    BOOST_SPIRIT_DEFINE(top, foo);  
  }  
}  
  
int main(int ac, char** av, char** ev)  
{  
  using boost::spirit::x3::ascii::space;  
  
  extbit::ast::top node;  
  extbit::parser::top foobar;  
  
  std::string nothing; // nothing is really needed  
  if (phrase_parse(nothing.begin(), nothing.end(), space, node)) {  
    // ...  
  }  
}  
```  
  
Or just from this links  
  * https://github.com/extbit/mold/blob/master/tests/foobar_bad.cpp  
  * https://github.com/extbit/mold/blob/master/tests/foobar_good.cpp  
  
Note that I just wrote this quickly to point it out, didn't spent time running it.

---

## Comment 3

> Username: djowel  
> Created at: 2019-09-11 01:51:35 UTC  
> Url: https://github.com/boostorg/spirit/issues/533#issuecomment-530185559  

Looks good, but please check the current set of tests and see where this is best placed. A PR would be much appreciated!

---

## Comment 4

> Username: duzy  
> Created at: 2019-09-11 02:32:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/533#issuecomment-530194082  

Hi @djowel , I'm afraid I didn't get it. What's a **PR** please, I will provide it as long as I can. :)

---

## Comment 5

> Username: djowel  
> Created at: 2019-09-11 02:50:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/533#issuecomment-530197106  

> Hi @djowel , I'm afraid I didn't get it. What's a **PR** please, I will provide it as long as I can. :)  
  
Oh a Pull Request: https://help.github.com/en/articles/about-pull-requests

---

## Comment 6

> Username: duzy  
> Created at: 2019-09-11 02:50:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/533#issuecomment-530197121  

While checking the current tests, I found `boost/libs/spirit/test/x3/optional.cpp` but different issue addressed. I suggest that this new test could be added to `boost/libs/spirit/test/x3/optional_ast_node.cpp` or similar. As it seems to happen when using a `boost::optional<foo>` as an ast, e.g. `x3::rule<class foo, boost::optional<ast::foo>>`.

---

## Comment 7

> Username: duzy  
> Created at: 2019-09-11 02:50:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/533#issuecomment-530197276  

@djowel ok, I'll try to do a **PR**.

---

## Comment 8

> Username: duzy  
> Created at: 2019-09-11 03:40:24 UTC  
> Updated at: 2019-09-11 03:42:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/533#issuecomment-530206269  

@djowel Please review the **PR** and do necessary modifications. Note that I didn't spend time to do a full running through the entire test set.  
  
https://github.com/boostorg/spirit/pull/534

---

## Comment 9

> Username: Kojoley  
> Created at: 2020-06-07 16:47:49 UTC  
> Updated at: 2020-06-08 16:23:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/533#issuecomment-640246813  

You should not be making `foo` rule attribute optional, here is the fixed grammar:  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
  
namespace extbit  
{  
  namespace ast  
  {  
    struct bar  
    {  
      int baz1;  
      int baz2;  
    };  
  
    using foo = boost::optional<bar>;  
  
    struct top  
    {  
      bar a;  
      foo b;  
      bar c;  
    };  
  }  
}  
  
BOOST_FUSION_ADAPT_STRUCT(extbit::ast::top, a, b, c)  
BOOST_FUSION_ADAPT_STRUCT(extbit::ast::bar, baz1, baz2)  
  
namespace extbit  
{  
  namespace parser  
  {  
    namespace x3 = boost::spirit::x3;  
  
    const x3::rule<class top, ast::top> top = "top";  
    const x3::rule<class foo, ast::bar> foo = "foo";  
    using x3::int_;  
  
    auto const top_def = foo >> -foo >> foo;  
    auto const foo_def = int_ >> int_;  
  
    BOOST_SPIRIT_DEFINE(top, foo);  
  }  
}  
  
int main(int ac, char** av, char** ev)  
{  
  using boost::spirit::x3::ascii::space;  
  
  extbit::ast::top node;  
  
  std::string nothing; // nothing is really needed  
  if (phrase_parse(nothing.begin(), nothing.end(), extbit::parser::top, space, node)) {  
    // ...  
  }  
}  
  
```  
https://wandbox.org/permlink/efiYZS3yvwp72FxJ  
  
```diff  
--- a.cpp	1970-01-01 00:00:00.000000000 +0000  
+++ b.cpp	1970-01-01 00:00:00.000000000 +0000  
@@ -32,7 +32,7 @@  
     namespace x3 = boost::spirit::x3;  
   
     const x3::rule<class top, ast::top> top = "top";  
-    const x3::rule<class foo, ast::foo> foo = "foo";  
+    const x3::rule<class foo, ast::bar> foo = "foo";  
     using x3::int_;  
   
     auto const top_def = foo >> -foo >> foo;  
@@ -47,10 +47,9 @@  
   using boost::spirit::x3::ascii::space;  
   
   extbit::ast::top node;  
-  extbit::parser::top foobar;  
   
   std::string nothing; // nothing is really needed  
-  if (phrase_parse(nothing.begin(), nothing.end(), space, node)) {  
+  if (phrase_parse(nothing.begin(), nothing.end(), extbit::parser::top, space, node)) {  
     // ...  
   }  
 }  
```

---

## Comment 10

> Username: duzy  
> Created at: 2020-06-08 02:48:32 UTC  
> Updated at: 2020-06-08 02:50:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/533#issuecomment-640331669  

@Kojoley It's been long time since I paste the example test code to reveal the issue. I'm not sure if I still remember everything. But I just go through everything to recall my memory and compared your fixed grammar against my original one. It appears to me that the real change that should be related to the `move_to` op should be:  
```c++  
    using foo = boost::optional<bar>;  
  
    struct top  
    {  
      bar a;  
      foo b;  
      bar c;  
    };  
```  
Being changed into this:  
```c++  
    struct top  
    {  
      bar a;  
      boost::optional<bar> b;  
      bar c;  
    };  
```  
  
The second change as  
```c++  
  if (phrase_parse(nothing.begin(), nothing.end(), space, node)) {  
    // ...  
  }  
```  
Into:  
```c++  
  if (phrase_parse(nothing.begin(), nothing.end(), extbit::parser::top, space, node)) {  
    // ...  
  }  
```  
Should be spelling error fix (didn't pass the parser argument).  
  
So I'm not sure if changing `using foo = boost::optional<bar>;` into a bare `boost::optional<bar>` let it compiles, but it's really making no sense as that `foo` should be identical to `boost::optional<bar>` for the case.  
  
If I remember this correctly, I think the original parser did not deal with the `boost::optional<T>` cases for `move_to` op. **That's the reason it an't compiles for storing data of grammar `-foo` into `boost::optional<foo>` value.**  
  
So it's very straight way to came up with the `move_to(boost::optional<Source>&& src, Dest& dest)` case. And _I do not agree with you if the solution is the client code should avoid using structure like `boost::optional<foo>` for storing data_.  
  
Please let me know if my understanding is not correct. It's been a long while since the commit, I might not remember it well. But I can still remember the `move_to` op with the `boost::optional` case from this thread.

---

## Comment 11

> Username: Kojoley  
> Created at: 2020-06-08 11:19:10 UTC  
> Updated at: 2020-06-08 11:19:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/533#issuecomment-640540916  

> If I remember this correctly, I think the original parser did not deal with the `boost::optional<T>` cases for move_to op.  
  
Yes, and it must not. If the value you are parsing into cannot accept `boost::optional` nothing should be done implicitly at this point, because not initializing the value most likely is not what the user wants and there is a mistake in the grammar.  
  
> So it's very straight way to came up with the `move_to(boost::optional<Source>&& src, Dest& dest)` case.  
  
It is not. And, if you really need that behavior for the whole library you may specialize `traits::transform_attribute`, this customization point is exactly for this purposes.  
  
> And I do not agree with you if the solution is the client code should avoid using structure like `boost::optional<foo>` for storing data.  
  
It is not what you have "fixed". `move_to` from optional to optional was working all the time.  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/optional.hpp>  
#include <boost/optional/optional_io.hpp>  
#include <iostream>  
  
namespace x3 = boost::spirit::x3;  
  
const x3::rule<class foo_r, boost::optional<int>> foo = "foo";  
auto const foo_def = x3::int_;  
BOOST_SPIRIT_DEFINE(foo)  
  
int main()  
{  
    std::string s = "123";  
    boost::optional<int> oi;  
    if (parse(s.begin(), s.end(), foo, oi))  
        std::cout << "success: " << oi << '\n';  
    else  
        std::cout << "failed";  
}  
```  
https://wandbox.org/permlink/3f9xgN08VlT26jsw

---

## Comment 12

> Username: duzy  
> Created at: 2020-06-08 14:18:35 UTC  
> Url: https://github.com/boostorg/spirit/issues/533#issuecomment-640647176  

> > If I remember this correctly, I think the original parser did not deal with the `boost::optional<T>` cases for move_to op.  
>   
> Yes, and it must not. If the value you are parsing into cannot accept `boost::optional` nothing should be done implicitly at this point, because not initializing the value most likely is not what the user wants and there is a mistake in the grammar.  
  
I think you didn't convinced me. The the real thing that matters for client code here is the `optional` field in the `struct top`, it's what the client code wanted. So this structure is a *MUST* for client:  
```c++  
struct top  
{  
    int a;  
    foo b;  
    int c;  
};  
```  
What I want was using the grammar `foo >> -foo >> foo` to parse data into `extbit::ast::top`, which is simply easy to understand, and it's easy to implement with `BOOST_FUSION_ADAPT_STRUCT` to tell x3 about the information of `struct top`. I don't see what mistake was in the grammar `foo >> -foo >> foo` for the `struct extbit::ast::top`?  
  
Nor I don't I understand what do you mean for "not initializing the value" in the case. The `optional` value is by default initialized to the default value of the first template parameter type. Nothing was not initialized!  
  
So in this grammar-ast binding of `foo >> -foo >> foo` to `struct top`, the declaration `BOOST_FUSION_ADAPT_STRUCT(extbit::ast::top, a, b, c)` should have tell all the required information!  
  
>   
> > So it's very straight way to came up with the `move_to(boost::optional<Source>&& src, Dest& dest)` case.  
>   
> It is not. And, if you really need that behavior for the whole library you may specialize `traits::transform_attribute`, this customization point is exactly for this purposes.  
  
I believe `traits::transform_attribute` should be designed for transforming attribute from the attribute type to a custom type. But it's obviously for client code, everything is better for using just a `BOOST_FUSION_ADAPT_STRUCT(extbit::ast::top, a, b, c)`:  
  
  * less code to be written and to maintained  
  * it's quite straightforward to work with `optional` as it's a predefined `boost` type support by x3  
  * seeing no strong intention for using `transform_attribute` for `optional` type   
  
> > And I do not agree with you if the solution is the client code should avoid using structure like `boost::optional<foo>` for storing data.  
>   
> It is not what you have "fixed". `move_to` from optional to optional was working all the time.  
  
I know it's working for `optional<int>`, but not working for the combination like in this case. `optional<int>` is different from `optional<bar>` and I think when it's a member of another custom structs, it's different again.  
  
So in my view, it's quite straightforward to *complete* the `move_to` op for the case instead of using more complex `transform_attribute` or something else.  
  
But I'm very interested to know if the specialization `void move_to(boost::optional<Source>&& src, Dest& dest)` caused any other issues. (see https://github.com/extbit/mold/commit/1d2978d588a86e0f70855bbc8fe5e1ced50596af#)  
  
Or if the new C++ standards (C++11, C++20, etc) might have caused new issues? (Especially the new `operator` overriding.)

---

## Comment 13

> Username: Kojoley  
> Created at: 2020-06-08 14:32:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/533#issuecomment-640664703  

> I think you didn't convinced me.  
  
I am sorry, but I should not convince you in anything. If you are saying that it is safe, it is you who has to prove it.  
  
Quoting https://github.com/boostorg/spirit/pull/534#issuecomment-640608946:  
  
> > I'm not sure what's real problem is by adding `boost:optional` LHS for `move_to` op.   
>   
> Here is the problem:  
>   
> ```cpp  
> #include <boost/spirit/home/x3.hpp>  
> #include <boost/optional.hpp>  
> #include <iostream>  
>   
> namespace x3 = boost::spirit::x3;  
>   
> const x3::rule<class foo_r, boost::optional<int>> foo = "foo";  
> auto const foo_def = x3::eps;  
> BOOST_SPIRIT_DEFINE(foo)  
>   
> int main()  
> {  
>     std::string s = "";  
>     int i;  
>     if (parse(s.begin(), s.end(), foo, i))  
>         std::cout << "success: " << i << '\n';  
>     else  
>         std::cout << "failed";  
> }  
> ```  
>   
> After this PR it compiles and it is invoking UB.

---

## Comment 14

> Username: duzy  
> Created at: 2020-06-08 14:43:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/533#issuecomment-640674172  

@Kojoley Check https://github.com/boostorg/spirit/pull/534#issuecomment-640667042 for the different of `int` and `optional<int>` for your `foo` parser.  
  
No worry about the safety of `move_to`, we know what it's doing and when when we should use it. But the code was actually long ago, I'm expecting things are changed a lot.

---

## Comment 15

> Username: Kojoley  
> Created at: 2020-06-08 16:53:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/533#issuecomment-640748593  

I cloned https://github.com/extbit/mold and tried to build the tests, while it is spitting a lot of unrelated errors, the one is related to Spirit is:  
```  
boost/spirit/home/x3/support/traits/move_to.hpp:196:24: error: no matching function for call to 'move_to(std::remove_reference<boost::optional<std::vector<mold::domain::tildache::ast::node> >&>::type, std::vector<mold::domain::tildache::ast::node>&, boost::mpl::identity<boost::spirit::x3::traits::container_attribute>::type)'  
```  
  
I have not investigated the grammar, but asking `boost::optional<std::vector<mold::domain::tildache::ast::node> >` to assign to `std::vector<mold::domain::tildache::ast::node>` is alarming, it is not that bad as in the example your have provided, but not much better either.

---

## Comment 16

> Username: Kojoley  
> Created at: 2020-06-08 18:23:28 UTC  
> Url: https://github.com/boostorg/spirit/issues/533#issuecomment-640794387  

I have extracted the grammar part that exhibits the error message above, it still needs an investigation to say anything.  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
#include <boost/optional.hpp>  
#include <vector>  
#include <iostream>  
  
namespace x3 = boost::spirit::x3;  
  
namespace ast {  
  
  struct node {};  
  struct expression {};  
  
  using node_list = std::vector<node>;  
  
  struct tild_expr_case  
  {  
    expression expr;  
    node_list nodes;  
  };  
  
  using tild_else_case = boost::optional<node_list>;  
  using tild_expr_cases = std::vector<tild_expr_case>;  
    
  struct tild_once_section  
  {  
    tild_expr_case expr_case;  
    tild_else_case else_case;  
  };  
}  
BOOST_FUSION_ADAPT_STRUCT(ast::tild_expr_case, expr, nodes)  
BOOST_FUSION_ADAPT_STRUCT(ast::tild_once_section, expr_case, else_case)  
  
namespace parser {  
    using x3::omit;  
    using x3::lit;  
    using x3::char_;  
    using x3::confix;  
  
    auto const sk = x3::skip(x3::space);  
  
    x3::rule<struct expression_class, ast::expression> const expression;  
    x3::rule<struct tild_once_section_class, ast::tild_once_section> const tild_once_section;  
    x3::rule<struct tild_once_case_class, ast::tild_expr_case> const tild_once_case;  
    x3::rule<struct tild_else_case_class, ast::tild_else_case> const tild_else_case;  
    x3::rule<struct node_list_class, ast::node_list> const node_list;  
    x3::rule<struct node_class, ast::node> const node;  
  
    auto const expression_def =  
      x3::attr(ast::expression{})  
      ;  
  
    auto const tild_once_case_def =  
      confix(lit("{{") >> sk[char_('~') >> "once"], sk["}}"])[ expression ]  
      >> node_list  
      ;  
  
    auto const tild_once_section_def =  
      tild_once_case  
      >> -tild_else_case  
      >> omit[sk[lit("{{") >> '~' >> "end" >> -lit("once") >> "}}"]]  
      ;  
  
    auto const tild_else_case_def =  
      omit[sk[lit("{{") >> '~' >> "else" >> "}}"]]  
      >> node_list  
      ;  
  
    auto const node_list_def =  
      *node  
      ;  
  
    auto const node_def =  
      x3::attr(ast::node{})  
      ;  
  
    BOOST_SPIRIT_DEFINE(node)  
    BOOST_SPIRIT_DEFINE(node_list)  
    BOOST_SPIRIT_DEFINE(tild_else_case)  
    BOOST_SPIRIT_DEFINE(tild_once_case)  
    BOOST_SPIRIT_DEFINE(tild_once_section)  
    BOOST_SPIRIT_DEFINE(expression)  
}  
  
int main()  
{  
    std::string s = "";  
    ast::tild_once_section v;  
    if (parse(s.begin(), s.end(), parser::tild_once_section, v))  
        std::cout << "success\n";  
    else  
        std::cout << "failed";  
}  
```

---

## Comment 17

> Username: Kojoley  
> Created at: 2020-06-08 18:48:51 UTC  
> Updated at: 2020-06-08 18:50:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/533#issuecomment-640806775  

And, there is a clear problem, exactly on the line you have pointed in the first post. The `tild_else_case` rule has an attribute of `ast::tild_else_case` which is an alias `using tild_else_case = boost::optional<node_list>`, while `-tild_else_case` parser has `optional<tild_else_case>` attribute.  
  
So, the problem is in your grammar, to fix it you need to move that `-` inside `tild_else_case_def`, like:  
```cpp  
    auto const tild_once_section_def =  
      tild_once_case  
      >> tild_else_case  
      >> omit[sk[lit("{{") >> '~' >> "end" >> -lit("once") >> "}}"]]  
      ;  
  
    auto const tild_else_case_def =  
      -(omit[sk[lit("{{") >> '~' >> "else" >> "}}"]]  
      >> node_list)  
      ;  
```  
or alternative fix is to change the attribute type of `tild_else_case` to `node_list`.  
  
Now, I am closing the issue as invalid.

---

## Comment 18

> Username: duzy  
> Created at: 2020-06-09 01:15:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/533#issuecomment-640970394  

> And, there is a clear problem, exactly on the line you have pointed in the first post. The `tild_else_case` rule has an attribute of `ast::tild_else_case` which is an alias `using tild_else_case = boost::optional<node_list>`, while `-tild_else_case` parser has `optional<tild_else_case>` attribute.  
>   
> So, the problem is in your grammar, to fix it you need to move that `-` inside `tild_else_case_def`, like:  
>   
> ```c++  
>     auto const tild_once_section_def =  
>       tild_once_case  
>       >> tild_else_case  
>       >> omit[sk[lit("{{") >> '~' >> "end" >> -lit("once") >> "}}"]]  
>       ;  
>   
>     auto const tild_else_case_def =  
>       -(omit[sk[lit("{{") >> '~' >> "else" >> "}}"]]  
>       >> node_list)  
>       ;  
> ```  
>   
> or alternative fix is to change the attribute type of `tild_else_case` to `node_list`.  
>   
> Now, I am closing the issue as invalid.  
  
Okay, thank you for coming up with this fix to the grammar. I'll mark a link to this post and investigate when next time working on that again.
