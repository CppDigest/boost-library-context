# #534 Add handling with `optional<Source>` for `boost::spirit::x3::traits::… [Merged]

> Username: duzy  
> Created at: 2019-09-11 03:37:56 UTC  
> Updated at: 2020-06-08 18:10:55 UTC  
> Merged at: 2019-09-11 06:48:59 UTC  
> Closed at: 2019-09-11 06:48:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/534  

…move_to`,  
  
so that optional ast node will work. See also test/x3/optional_ast_node.cpp for  
the case.  
  
Addressed at https://github.com/boostorg/spirit/issues/533

---

## Comment 1

> Username: djowel  
> Created_at: 2019-09-11 06:48:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/534#issuecomment-530246573  

Wonderful. Thanks!

---

## Comment 2

> Username: Kojoley  
> Created_at: 2020-06-07 16:21:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/534#issuecomment-640243230  

@djowel Why you let this get in? In my opinion it must not compile and it was, now this allows to shoot the foot off. It just happens that in the example/test definition of   
`twoints` always sets the value, but `optional` tells that it may not, and in this case reading `adata::c` or copying the whole `adata` structure is UB.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2020-06-07 16:34:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/534#issuecomment-640245019  

Well, and the added test does not compile, it was not added to `Jamfile` to be run.

---

## Comment 4

> Username: djowel  
> Created_at: 2020-06-07 18:33:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/534#issuecomment-640260612  

@Kojoley I'm probably not understanding the deeper issues here, so please go ahead with your revert.

---

## Comment 5

> Username: duzy  
> Created_at: 2020-06-08 02:55:01 UTC  
> Updated_at: 2020-06-08 02:56:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/534#issuecomment-640333489  

@Kojoley I just responded from the thread https://github.com/boostorg/spirit/issues/533#issuecomment-640331669 . I'm not sure what's real problem is by adding `boost:optional` LHS for `move_to` op. If you can paste the code to show the case, I'm very interested to review it for the problem.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2020-06-08 13:38:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/534#issuecomment-640608946  

> I'm not sure what's real problem is by adding `boost:optional` LHS for `move_to` op.   
  
Here is the problem:  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/optional.hpp>  
#include <iostream>  
  
namespace x3 = boost::spirit::x3;  
  
const x3::rule<class foo_r, boost::optional<int>> foo = "foo";  
auto const foo_def = x3::eps;  
BOOST_SPIRIT_DEFINE(foo)  
  
int main()  
{  
    std::string s = "";  
    int i;  
    if (parse(s.begin(), s.end(), foo, i))  
        std::cout << "success: " << i << '\n';  
    else  
        std::cout << "failed";  
}  
```  
  
After this PR it compiles and it is invoking UB.

---

## Comment 7

> Username: duzy  
> Created_at: 2020-06-08 14:34:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/534#issuecomment-640667042  

> > I'm not sure what's real problem is by adding `boost:optional` LHS for `move_to` op.  
>   
> Here is the problem:  
>   
> ```c++  
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
  
If I understand it correctly, the `eps` parses nothing (zero data). The grammar rule `foo` in this case should be undefined behavior:  
  
    * eps is not a `int` parser, nor a `optional<int>` parser  
    * the `foo` grammar is not a `int` parser, but a `optional<int>` parser  
  
So in your example,  `parse(s.begin(), s.end(), foo, i)` should be undefined (I'm not sure if it's what you mean by *UB*?).  
  
What if you try with this:  
```c++  
#include <boost/spirit/home/x3.hpp>  
#include <boost/optional.hpp>  
#include <iostream>  
  
namespace x3 = boost::spirit::x3;  
  
const x3::rule<class foo_r, boost::optional<int>> foo = "foo";  
auto const foo_def = x3::eps;  
BOOST_SPIRIT_DEFINE(foo)  
  
int main()  
{  
    std::string s = "";  
    boost::optional<int> i;  
    if (parse(s.begin(), s.end(), foo, i))  
        std::cout << "success: " << i << '\n';  
    else  
        std::cout << "failed";  
}  
```  
  
Noted that `foo` is for parsing `optional<int>` not `int`, what do you expect with it for a `int`??

---

## Comment 8

> Username: Kojoley  
> Created_at: 2020-06-08 14:47:11 UTC  
> Url: https://github.com/boostorg/spirit/pull/534#issuecomment-640676383  

You are focusing on the wrong thing, there is no problem with `eps`, you can replace it with `-int_` and it will be the same thing, I used `eps` to show the extreme absurdity of the situation.  
  
If you need an analogy, what you have done in the PR is similarly to allow implicit pointer to reference conversion.

---

## Comment 9

> Username: duzy  
> Created_at: 2020-06-08 15:10:20 UTC  
> Updated_at: 2020-06-08 15:12:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/534#issuecomment-640691153  

> You are focusing on the wrong thing, there is no problem with `eps`, you can replace it with `-int_` and it will be the same thing, I used `eps` to show the extreme absurdity of the situation.  
>   
> If you need an analogy, what you have done in the PR is similarly to allow implicit pointer to reference conversion.  
  
Are you saying that the PR makes this undefined code passed compilation?  
```c++  
int main()  
{  
    std::string s = "";  
    int i;  
    if (parse(s.begin(), s.end(), foo, i))  
        std::cout << "success: " << i << '\n';  
    else  
        std::cout << "failed";  
}  
```  
  
Since the `foo` parser is expecting to work with `optional<int>`, when parse it for `int`, it should complain for errors. If the PR makes it pass compilation, it should need a new fix or better fix instead of reverting (because it disabled the `optional<bar>` field again).  
  
A proper try might be like this:  
```c++  
    template <typename Source, typename Dest>  
    inline void move_to(boost::optional<Source>&& src, boost::optional<Dest>&& dest)  
    {  
        if (src) detail::move_to(std::move(*src), dest  
          , typename attribute_category<Dest>::type());  
    }  
```  
To replace this:  
```c++  
    template <typename Source, typename Dest>  
    inline void move_to(boost::optional<Source>&& src, Dest&& dest)  
    {  
        if (src) detail::move_to(std::move(*src), dest  
          , typename attribute_category<Dest>::type());  
    }  
```  
So that a `optional<int>` parser won't work with `int` again (by mistake).  
  
Anyway it's up to you, I'm so glad that you point it out.

---

## Comment 10

> Username: Kojoley  
> Created_at: 2020-06-08 15:22:16 UTC  
> Updated_at: 2020-06-08 15:22:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/534#issuecomment-640698269  

> Are you saying that the PR makes this undefined code passed compilation?  
  
Yes, in every my message, and you are saying about it:  
  
https://github.com/boostorg/spirit/issues/533#issuecomment-640674172:  
> No worry about the safety of `move_to`, we know what it's doing and when when we should use it.  
  
---  
  
> A proper try might be like this:  
> ```cpp  
>     template <typename Source, typename Dest>  
>     inline void move_to(boost::optional<Source>&& src, boost::optional<Dest>&& dest)  
>     {  
>         if (src) detail::move_to(std::move(*src), dest  
>           , typename attribute_category<Dest>::type());  
>     }  
> ```  
  
There is no need in it, see example in https://github.com/boostorg/spirit/issues/533#issuecomment-640540916, the https://github.com/boostorg/spirit/blob/e01e9fbfdaf2c16332c4da89e9b563ee70ea95e9/include/boost/spirit/home/x3/support/traits/move_to.hpp#L155-L160 handles it since the beginning.

---

## Comment 11

> Username: duzy  
> Created_at: 2020-06-08 15:50:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/534#issuecomment-640714706  

> > Are you saying that the PR makes this undefined code passed compilation?  
>   
> Yes, in every my message, and you are saying about it:  
>   
> [#533 (comment)](https://github.com/boostorg/spirit/issues/533#issuecomment-640674172):  
>   
> > No worry about the safety of `move_to`, we know what it's doing and when when we should use it.  
>   
> > A proper try might be like this:  
> > ```c++  
> >     template <typename Source, typename Dest>  
> >     inline void move_to(boost::optional<Source>&& src, boost::optional<Dest>&& dest)  
> >     {  
> >         if (src) detail::move_to(std::move(*src), dest  
> >           , typename attribute_category<Dest>::type());  
> >     }  
> > ```  
>   
> There is no need in it, see example in [#533 (comment)](https://github.com/boostorg/spirit/issues/533#issuecomment-640540916), the  
>   
> https://github.com/boostorg/spirit/blob/e01e9fbfdaf2c16332c4da89e9b563ee70ea95e9/include/boost/spirit/home/x3/support/traits/move_to.hpp#L155-L160  
>   
> handles it since the beginning.  
  
Thanks @Kojoley , it's up to you to decide how to fix it. But notice that `move_to(Source&& src, Dest& dest, optional_attribute)` require a extra attribute argument, which means it requires extra settings from client code. In your point of view, it should be done via something like `transform_attribute`, but in our case or for complex/large AST, we need it exactly to support that `optional<bar>` struct member to be passed.  
  
But when I said "no worry about the safety of move_to", there's no offense. I mean it for real. Spirit X3 is perfect for us to quickly prototype a grammar for parsing or for quick utility. Normally we expect errors to be raised as soon as possible. It's obviously the best time to complain about errors at compile time. Or we prefer to see it crash as soon as possible if there's errors. When we change `move_to` for the fix, we know that it's for our case specifically, so we know what's happening (rather than using an `optional<int>` parser for an `int`).  
  
The complexity of spirit::x3 is actually driven us to write language parsing code in C++ from scratch. For large AST and complex semantics, spirit::x3 is not a good choice, it mixes everything and difficult error tracking spent us more time than self-written language parsing.  
  
Thant's why I would suggest you to simply spirit::x3 as much as possible, instead of introducing too many settings like `transform_attribute`. It's really not a good option for large AST tree.

---

## Comment 12

> Username: Kojoley  
> Created_at: 2020-06-08 16:24:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/534#issuecomment-640733477  

It seems that you still not understand the issue. The point why `optional<int>` should not parse into `int` is because `optional<int>` has no implicit conversion to `int`, i.e.:  
```cpp  
boost::optional<int> foo();  
int i = foo();  
```  
will not compile, because authors of `optional` decided to not allow it, and for good reason. If you knew upfront that the `optional<T>` will always has a value in it, you simply do not need `optional`, and could just replace the type with the plain `T`, but if you did not, it means that there is something broken with your types or grammar.  
  
I am open to simplifications in the library, if you have a justified examples I am happy to try to make them working.

---

## Comment 13

> Username: duzy  
> Created_at: 2020-06-08 17:53:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/534#issuecomment-640779782  

> It seems that you still not understand the issue. The point why `optional<int>` should not parse into `int` is because `optional<int>` has no implicit conversion to `int`, i.e.:  
>   
> ```c++  
> boost::optional<int> foo();  
> int i = foo();  
> ```  
>   
> will not compile, because authors of `optional` decided to not allow it, and for good reason. If you knew upfront that the `optional<T>` will always has a value in it, you simply do not need `optional`, and could just replace the type with the plain `T`, but if you did not, it means that there is something broken with your types or grammar.  
  
You're right, `optional` is actually more like a special *pointer* or *reference*. I think I was actually talking about `variant` when I said 'initialized into the first template type' (see https://github.com/duzy/mold/blob/caf55317662573380333c56105a8e0de08871f13/include/mold/domain/tildache/ast.hpp#L62). Because the example of the *mold* project was actually written in around 2016 for a quick utility. It's been years, so I have to think and recall the memory. I can only think of more when going back to and reading the code while reading your words to find out what's happening.  
  
The truth in our case was that when we're using `optional`, we really want it to be an *option* to be absent. This is exactly the semantic of the language ast of `optional`. So the usage of `optional` is correct for our case.  
  
But if the PR is making an `optional<int>` parser generating for `int`, it's obviously best to restrict it at compile time (it's properly depending on whether if an `int` is movable into an `optional<int>` (the && semantic)). The semantics of `-foo` for generating `using foo = optional<bar>` (in my case as a struct member) is indeed a natural needs for simplification without the needs for extra settings like `transform_attribute` or other specializations/overriding. So if will be great if you can come up with a fix to consider the both.  
  
I think narrowing down the `Dest` type for `move_to` could be a solution to disable the undefined-behavior code at compile time.  
  
>   
> I am open to simplifications in the library, if you have a justified examples I am happy to try to make them working.  
  
I wish I could try something for the moment, but it's been changed a lot of my situation. I think my last change related to this should be https://github.com/extbit/mold/commit/1d2978d588a86e0f70855bbc8fe5e1ced50596af, and the example test should address the failure issue (if not applying this PR):  
```c++  
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
```

---

## Comment 14

> Username: Kojoley  
> Created_at: 2020-06-08 18:00:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/534#issuecomment-640783421  

Once again, your example is ill-formed (I showed a fix in https://github.com/boostorg/spirit/issues/533#issuecomment-640246813), in `top_def = foo >> -foo >> foo;` the third `foo` parses into `bar`, it asks to parse `optional<bar>` into `bar`!

---

## Comment 15

> Username: duzy  
> Created_at: 2020-06-08 18:10:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/534#issuecomment-640788357  

> Once again, your example is ill-formed (I showed a fix in [#533 (comment)](https://github.com/boostorg/spirit/issues/533#issuecomment-640246813)), in `top_def = foo >> -foo >> foo;` the third `foo` parses into `bar`, it asks to parse `optional<bar>` into `bar`!  
  
Okay, please go with your fix on that. It was spelling error not unrelated to the real issue. Last time I wrote this example was just quickly extracting the case from _mold_ to show the issue.

---
