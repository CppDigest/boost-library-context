# #444 - X3: Rules do not pass-through attributes [Closed]

> Username: Xeverous  
> Created at: 2019-01-30 21:43:24 UTC  
> Updated at: 2019-02-12 19:13:10 UTC  
> Closed at: 2019-02-09 12:49:59 UTC  
> Url: https://github.com/boostorg/spirit/issues/444  

IMO this is an unwanted bug, as copies of various AST subparts can be expensive. It's also nowhere stated in the documentation that types need to be copyable.  
  
I don't have an MCVE now (ask me if you want any, I will try to reduce my project as long as it reproduces). The error message (template backtrace) points to spirit implementation:  
  
    ../src/parser/ast.hpp:243:8:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::call_rule_definition(const RHS&, const char*, Iterator&, const Iterator&, const Context&, ActualAttribute&, ExplicitAttrPropagation) [...]  
    [...]/boost/spirit/home/x3/nonterminal/rule.hpp:61:39:   required from 'bool boost::spirit::x3::rule_definition<ID, RHS, Attribute, force_attribute_>::parse(Iterator&, const Iterator&, const Context&, boost::spirit::x3::unused_type, Attribute_&) const [...]  
    ../src/parser/grammar_def.hpp:256:1:   required from 'bool fs::parser::parse_rule(fs::parser::grammar_type, Iterator&, const Iterator&, const Context&, Attribute&) [...]  
    ../src/parser/grammar.cpp:13:1:   required from here  
    [...]/mingw64/include/c++/8.2.0/bits/stl_construct.h:75:7: error: use of deleted function 'fs::parser::ast::action_expression::action_expression(const fs::parser::ast::action_expression&)'  
  
digging the implementation (`x3/nonterminal/rule.hpp`, lines 56-66):  
  
        template <typename Iterator, typename Context, typename Attribute_>  
        bool parse(Iterator& first, Iterator const& last  
          , Context const& context, unused_type, Attribute_& attr) const  
        {  
            return detail::rule_parser<attribute_type, ID>  
                ::call_rule_definition(  
                    rhs, name, first, last  
                  , context  
                  , attr  
                  , mpl::bool_<force_attribute>());  
        }  
  
then (`x3/nonterminal/detail/rule.hpp`, lines 295-349)  
  
        template <typename RHS, typename Iterator, typename Context  
          , typename ActualAttribute, typename ExplicitAttrPropagation>  
        static bool call_rule_definition(  
            RHS const& rhs  
          , char const* rule_name  
          , Iterator& first, Iterator const& last  
          , Context const& context, ActualAttribute& attr  
          , ExplicitAttrPropagation)  
        {  
            boost::ignore_unused(rule_name);  
  
            typedef traits::make_attribute<Attribute, ActualAttribute> make_attribute;  
  
            // do down-stream transformation, provides attribute for  
            // rhs parser  
            typedef traits::transform_attribute<  
                typename make_attribute::type, Attribute, parser_id>  
            transform;  
  
            typedef typename make_attribute::value_type value_type;  
            typedef typename transform::type transform_attr;  
            value_type made_attr = make_attribute::call(attr); // HERE IS THE COPY  
            transform_attr attr_ = transform::pre(made_attr);  
  
            bool ok_parse  
              //Creates a place to hold the result of parse_rhs  
              //called inside the following scope.  
              ;  
            {  
             // Create a scope to cause the dbg variable below (within  
             // the #if...#endif) to call it's DTOR before any  
             // modifications are made to the attribute, attr_ passed  
             // to parse_rhs (such as might be done in  
             // traits::post_transform when, for example,  
             // ActualAttribute is a recursive variant).  
    #if defined(BOOST_SPIRIT_X3_DEBUG)  
                context_debug<Iterator, transform_attr>  
                dbg(rule_name, first, last, attr_, ok_parse);  
    #endif  
                ok_parse = parse_rhs(rhs, first, last, context, attr_, attr_  
                   , mpl::bool_  
                     < (  RHS::has_action  
                       && !ExplicitAttrPropagation::value  
                       )  
                     >()  
                  );  
            }  
            if (ok_parse)  
            {  
                // do up-stream transformation, this integrates the results  
                // back into the original attribute value, if appropriate  
                traits::post_transform(attr, std::forward<transform_attr>(attr_));  
            }  
            return ok_parse;  
        }  
  
`x3/support/traits/make_attribute`  
  
    template <typename Attribute>  
    struct make_attribute_base  
    {  
        static Attribute call(unused_type)  
        {  
             // synthesize the attribute/parameter  
            return Attribute();  
        }  
  
        template <typename T>  
        static T& call(T& value)  
        {  
            return value; // just pass the one provided  
        }  
    };  
  
The core of the problem lies on these 2 lines  
  
        value_type made_attr = make_attribute::call(attr);  
        transform_attr attr_ = transform::pre(made_attr);  
  
`make_attribute::call()` default-constructs the attribute if originally it was unused, but copies it instead if it was. At first I though that the library wants to preserve the state of the attribute (in case it's already partially filled) but ... no, **the copy that is just made is then passed to `transform::pre` which doesn't do anything with the argument and returns a default-constructed object of type `transform_attr`. `made_attr` is never used anymore which makes it essentially a useless copy**. At the end, `attr` (the orginal user object) is move-constructed from `attr_`.

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-01-30 22:19:04 UTC  
> Url: https://github.com/boostorg/spirit/issues/444#issuecomment-459132711  

I also spotted this while [answering this question](https://stackoverflow.com/questions/53320015/virtual-classes-as-ast-nodes-with-spirit/53329882#53329882) (you can look there to find a workaround). This should be copied from Qi and I am not sure why Qi having it. May be it was copied to `rule` from `action` without a reason or... because `transform_attribute` have to pass-trough as reference but actually in Qi it creates a value. Last time I was trying to modify Qi to make rules to pass-through attribute reference it turns to be a very big change and still a lot of work left so I gave up, but for X3 I think it should be much easier. The other thing is that `make_attribute` looks redundant even in `action` because `transform_attribute::pre` also returns a default constructed value if actual attribute type differs. https://github.com/boostorg/spirit/blob/b4c5ef702bf6c28e964a84c9e9abe1a6549bce69/include/boost/spirit/home/x3/nonterminal/detail/transform_attribute.hpp#L25

---

## Comment 2

> Username: Xeverous  
> Created at: 2019-01-31 09:21:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/444#issuecomment-459273831  

Thanks for the answer and the info, but unfortunately the workaround by hijacking `make_attribute` trait through specialization does not work. Apparently Spirit copies in more places.  
  
Now I hit this:  
  
    namespace boost { namespace spirit { namespace x3 { namespace traits  
    {  
        template <typename T, typename Enable = void>  
        struct value_initialize  
        {  
            static T call()  
            {  
                return boost::value_initialized<T>();  
            }  
        };  
    }}}}  
  
`value_initialized` is a class that simply holds `T`. The struct has defined user convertion to `T&` and `const T&` which is applied upon function return which copies the value. I don't get why it is used here - why don't just `return T()`?  
  
___  
  
Now regarding the `make_attribute` - is this ever needed? All the library wants is to apply type transformations to make attribute match user expectations. But it does not do it purely through traits but functions which return/create copies of the value. Actually the library is lucky in most places due to implicit moves on the returned object. The code above is one of such places where it is not that lucky.

---

## Comment 3

> Username: Xeverous  
> Created at: 2019-01-31 10:20:01 UTC  
> Updated at: 2019-01-31 10:33:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/444#issuecomment-459291888  

namespace boost::spirit::x3::traits {  
    template <>  
    struct value_initialize<ast::condition_expression, void>  
    {  
        static ast::condition_expression call()  
        {  
            return {}; // instead of return boost::value_initialized<T>()  
        }  
    };  
    }  
  
This + `make_attribute` specialization workarounds the bug

---

## Comment 4

> Username: Kojoley  
> Created at: 2019-01-31 13:51:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/444#issuecomment-459351049  

> `value_initialized` ... I don't get why it is used here - why don't just return T()?  
  
https://www.boost.org/doc/libs/1_69_0/libs/utility/value_init.htm, most likely it was copied from Qi; IIUC since C++11 it is obsolete and can be replaced with brace initialization (value_init documentation has [reference 1](https://www.boost.org/doc/libs/1_69_0/libs/utility/value_init.htm#references) to brace initialization paper) though it is not exactly the same thing.  
  
> Now regarding the `make_attribute` - is this ever needed?  
  
Yes and no. Yes - it is needed for actions when there is no actual value. No - it can be (and in X3 it is) done in `transform::pre` as I said above.

---

## Comment 5

> Username: Xeverous  
> Created at: 2019-01-31 14:47:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/444#issuecomment-459370713  

Can you estimate how much would be needed to be done to refactor things like `make_attribute::call()` so that only pure traits are used with no value copies? I might make a pull request for it but I don't think I have fully grasped what is the purpose of them.

---

## Comment 6

> Username: Kojoley  
> Created at: 2019-01-31 15:07:41 UTC  
> Url: https://github.com/boostorg/spirit/issues/444#issuecomment-459378298  

It should be a trivial PR if there are no pitfalls. Make `transform::pre` use `value_initialize` trait, pass the argument directly to `transform::pre`, remove `make_attribute` trait, change `value_type` usages to `type` (or may be even auto&&?) remove `value_type` typedef from `transform` (and `type` if auto&& works). But there is a side problem, it is a breaking change, though I think it is permissible here (I believe `make_attribute` was almost always used for workarounds, they can be ported to `transform`).

---

## Comment 7

> Username: cppljevans  
> Created at: 2019-01-31 15:37:21 UTC  
> Updated at: 2019-01-31 19:18:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/444#issuecomment-459389614  

> It should be a trivial PR if there are no pitfalls.  
  
[snip]  
  
And Xeverous, if you do make the PR, it would relieve some  
of the pressure Nikita might feel to do it.  Nikita's been  
doing most if not **all** the PR's recently (for the last  
year or so) and he probably would appreciate some help.  
  
Also, since you would be newly aware of the difficulty  
of understanding the code without adequate documentation,   
you could alleviate future such problems by providing better  
documentation using this newly, and probably painfully  
acquired, **valuable** knowledge!!!

---

## Comment 8

> Username: djowel  
> Created at: 2019-01-31 15:44:38 UTC  
> Updated at: 2019-01-31 19:18:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/444#issuecomment-459392499  

I always wished I had more time. I'm sorry for the inadequate documentation. Nevertheless, I'm still monitoring these threads and feel free to ask any direct questions and I'll try my best to answer them, or at least provide leads to some answers.  
  
And as always, I appreciate Nikita's wonderful work!

---

## Comment 9

> Username: Xeverous  
> Created at: 2019-01-31 16:43:09 UTC  
> Updated at: 2019-01-31 19:18:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/444#issuecomment-459415247  

> you could alleviate future such problems by providing better  
> documentation using this newly, and probably painfully  
> acquired, **valuable** knowledge!!!  
  
Ahh, this is so true. I have found multiple inaccuracies in examples or stuff that is missing, especially for new people. I have started my journey with X3 directly, without any prior experience with previous versions and even by looking at documentation volume I was confused whether X3 is just the parser or actually Spirit V2 so well simplified. And the fact that the last post on Spirit's own website is from CppNow 2015.  
  
So, to briefly reveal my painfully acquired knowledge - things that I really found valuable but nowhere mentioned in documentation:  
- X3 is a header only library ... as long as you `#define BOOST_SPIRIT_X3_NO_FILESYSTEM` or otherwise you get linker errors and wonder what actually tries to use filesystem API in the parser implementation  
- Something than can really confuse people who try to build examples using 2 handlers: `x3::annotate_on_success` uses `error_handler_tag` to retrieve injected data from context and then uses `.tag()` on it which does not match `.annotate()` that is offered by `x3::position_cache`. If you try to build up a full program of snippets presented in the annotations example it will not compile. I currently 'workarounded' this issue by copy-pasting X3's eror handler completely into my project which also allows me to better customize error messages (IMO the interface could really get some improvements).  
- There is no information how to use nested contexts. Doc mentions you can do `x3::with<>()[ x3::with<>()[ parser::grammar() ] ]` but there is no information how the config file (specifically `context_type`) should be defined.  
- I have a suspicion that there is something wrong with `x3::context` definition itself because the code compiles but my IDE underlines almost any instantiation of it as invalid template arguments. I guess this is related to `const unused_type` issue.

---

## Comment 10

> Username: cppljevans  
> Created at: 2019-01-31 18:25:40 UTC  
> Updated at: 2019-01-31 19:18:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/444#issuecomment-459452024  

.  
.  
.  
>     * I have a suspicion that there is something wrong with `x3::context` definition itself because the code compiles but my IDE underlines almost any instantiation of it as invalid template arguments. I guess this is related to `const unused_type` issue.  
  
Could you be more specific by, for example, by providing a  
link to the `const unused_type` issue and possibly the code  
exhibiting the problem?

---

## Comment 11

> Username: Kojoley  
> Created at: 2019-01-31 19:18:11 UTC  
> Updated at: 2019-01-31 19:18:43 UTC  
> Url: https://github.com/boostorg/spirit/issues/444#issuecomment-459470564  

1) Most of Boost libraries are header-only, so the libraries that require building are exceptions and there is a list of them https://www.boost.org/doc/libs/1_69_0/more/getting_started/windows.html#header-only-libraries there also note on https://www.boost.org/doc/libs/1_69_0/libs/spirit/doc/html/spirit/structure/include.html but not in X3 documentation itself.  
  Actually I would like to remove occasional filesystem usages as well as RTTI (there are cheaper ways to do the same), but I am mostly spending time in fixing bugs right now.  
2) If you see that something can be improved you can open an issue for it.  
3) There was somewhere post from @djowel on how to use compiler error to show the context type, but `decltype` is your best friend here (you can even make a prebuild step with it that will generate a header file with a correct instantiation type for you).  
4) Most likely your IDE have a bad highlighter, since C++11 you need a real compiler to parse C++ (because of constexprs and autos).  
  
I am going to hide off-topic messages, please open a PR for documentation problems.

---

## Comment 12

> Username: Xeverous  
> Created at: 2019-02-01 08:52:41 UTC  
> Updated at: 2019-02-01 13:30:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/444#issuecomment-459650844  

@Kojoley regarding 4: I'm using Eclipse CDT and it has a really good built-in parser, it does parse macros (I can even fully browse ~1500 expansion steps of fusion adapt macro), templates and correctly recognizes SFINAE so if there is anything that it highlights as error it is almost always a true compiler error unless it encountered something that is not yet supported (C++14 user-defined literals and C++17 CTAD and fold expressions). Following the code with the parser has actually helped me to track down what exactly in Spirit source code copies attributes. I will try to track the issue with `context_type` to find when and why it is underlined.

---

## Comment 13

> Username: Xeverous  
> Created at: 2019-02-01 08:54:54 UTC  
> Updated at: 2019-02-01 13:30:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/444#issuecomment-459651415  

@cppljevans https://stackoverflow.com/a/54095167/4818802

---

## Comment 14

> Username: Xeverous  
> Created at: 2019-02-01 13:04:09 UTC  
> Updated at: 2019-02-01 14:06:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/444#issuecomment-459716038  

Minimal code that reproduces the issue:  
  
    #include <boost/spirit/home/x3.hpp>  
    #include <boost/fusion/include/adapt_struct.hpp>  
    #include <iostream>  
    #include <vector>  
  
    namespace x3 = boost::spirit::x3;  
  
    namespace ast  
    {  
        struct block  
        {  
            block() = default;  
  
            block(const block&) = delete;  
            block& operator=(const block&) = delete;  
  
            block(block&&) = default;  
            block& operator=(block&&) = default;  
  
            int number = -1;  
            std::vector<char> chars;  
            std::vector<block> nested_blocks;  
        };  
  
        using ast_type = block;  
    }  
  
    BOOST_FUSION_ADAPT_STRUCT(ast::block, number, chars, nested_blocks)  
  
    using block_type = x3::rule<struct block_class, ast::block>;  
    BOOST_SPIRIT_DECLARE(block_type)  
  
    const block_type block = "block";  
    const auto block_def = x3::int_ > '{' > (x3::char_ % ',') > *block > '}';  
    BOOST_SPIRIT_DEFINE(block)  
  
    int main()  
    {  
        std::string input = R"(  
        1 {  
            a, b, c  
              
            11 {  
                d, e, f  
            }  
        }  
        2 {  
            x, y, z  
        })";  
  
        auto first = input.begin();  
        const auto last = input.end();  
  
        ast::ast_type ast;  
        if (x3::phrase_parse(first, last, block, x3::space, ast)) {  
            std::cout << "success:\n";  
        }  
        else {  
            std::cout << "fail!\n";  
        }  
    }  
  
I thought about adding test with this code but in this case it's not about whether test passes or not but whther is compiles or not.

---

## Comment 15

> Username: cppljevans  
> Created at: 2019-02-01 13:27:11 UTC  
> Updated at: 2019-02-01 13:31:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/444#issuecomment-459722041  

> Minimal code that reproduces the issue:  
  
{snip-of-code}  
  
> I thought about adding test with this code but in this case it's not about whether test passes or not but whther is compiles or not.  
  
My compile of this code shows the compile fails because ast has no default CTOR.  
How is that related to some issue with `context_type` or `unused_type`?

---

## Comment 16

> Username: Kojoley  
> Created at: 2019-02-01 13:30:39 UTC  
> Updated at: 2019-02-01 13:31:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/444#issuecomment-459722996  

@cppljevans context stuff is not about the original issue, it is an offtopic. The MWE @Xeverous provided is about the original issue.

---

## Comment 17

> Username: Xeverous  
> Created at: 2019-02-01 14:06:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/444#issuecomment-459733227  

@cppljevans edited example, added explicit default ctor

---

## Comment 18

> Username: Kojoley  
> Created at: 2019-02-09 12:49:59 UTC  
> Url: https://github.com/boostorg/spirit/issues/444#issuecomment-462041988  

Fixed by #449 + #452.
