# #453 - X3: BOOST_SPIRIT_INSTANTIATE regression [Closed]

> Username: Xeverous  
> Created at: 2019-02-06 23:42:56 UTC  
> Updated at: 2019-02-09 01:33:21 UTC  
> Closed at: 2019-02-08 11:51:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/453  

after a3cf3f2c3 X3 requires existing code to change grammar to add more `const x3::unused_type` workarounds  
  
This compiles fine just before a3cf3f2c3:  
  
```cpp  
    #include <boost/spirit/home/x3.hpp>  
  
    namespace x3 = boost::spirit::x3;  
  
    using comment_type = x3::rule<class comment_class, /* const */ x3::unused_type>;  
    BOOST_SPIRIT_DECLARE(comment_type)  
  
    using identifier_type = x3::rule<class identifier_class, std::string>;  
    BOOST_SPIRIT_DECLARE(identifier_type)  
  
    using whitespace_type = x3::rule<class whitespace_class, /* const */ x3::unused_type>;  
    BOOST_SPIRIT_DECLARE(whitespace_type)  
  
    using grammar_type = x3::rule<class grammar_class, std::string>;  
    BOOST_SPIRIT_DECLARE(grammar_type)  
  
    using skipper_type = whitespace_type;  
    using iterator_type = std::string::const_iterator;  
    using context_type = x3::phrase_parse_context<skipper_type>::type;  
  
    const comment_type comment = "comment";  
    const auto comment_def = x3::lexeme['#' >> *(x3::char_ - '\n')];  
    BOOST_SPIRIT_DEFINE(comment)  
  
    const identifier_type identifier = "identifier";  
    const auto identifier_def = x3::lexeme[(x3::alpha | '_') >> *(x3::alnum | '_')];  
    BOOST_SPIRIT_DEFINE(identifier)  
  
    const whitespace_type whitespace = "whitespace";  
    const auto whitespace_def = x3::space;  
    BOOST_SPIRIT_DEFINE(whitespace)  
  
    const grammar_type grammar = "code";  
    const auto grammar_def = identifier > comment;  
    BOOST_SPIRIT_DEFINE(grammar)  
  
    BOOST_SPIRIT_INSTANTIATE(grammar_type, iterator_type, context_type)  
```  
  
but after that commit, **all** grammars that do not syntesize any attribute have to specify `const x3::unused_type` (uncomment `const`)  
  
Unfortunately the pull mentioned in https://stackoverflow.com/a/54095167/4818802 does not solve the problem, it only requires code to have more workarounds than previously.

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-02-07 00:02:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/453#issuecomment-461237787  

> X3 requires existing code to change grammar to add more `const x3::unused_type` workarounds  
  
I do not see where you added more workarounds.  
  
The problem seems that you have to remove `const` after the change and that was intended (actually you need to completely remove the third parameter). I did not add `unused_type const` specialization because of: 1) maintaining backward comparability with such workaround will make code more complex without much need 2) that workaround is fragile as it makes `has_attribute == true`.  
  
The users that upgrade to a new version can just remove it.  
  
Try to add this:  
  
```cpp  
boost::spirit::x3::detail::rule_attr_deducer<unused_type const>  
  : rule_attr_deducer<unused_type> {};  
```  
  
If it will work for you I will add it not to bother people with changing their code.

---

## Comment 2

> Username: Kojoley  
> Created at: 2019-02-07 00:15:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/453#issuecomment-461240759  

I tried the `const` workaround and it looks like it still works. Your code seems to uncover some other bug, will look into it.

---

## Comment 3

> Username: Kojoley  
> Created at: 2019-02-07 00:28:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/453#issuecomment-461243644  

Well, that's exactly what I meant to ban: if a parser with has_attribute==true assigns to a rule - that's look suspicious and there should be missing `omit`, but now I see that requiring `omit` is not practical.

---

## Comment 4

> Username: Kojoley  
> Created at: 2019-02-07 00:47:37 UTC  
> Updated at: 2019-02-07 00:49:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/453#issuecomment-461247658  

Hmm, something strange happens here, I will need more time to dig into this. `BOOST_SPIRIT_INSTANTIATE(grammar_type, iterator_type, context_type)` somehow leads to instantiation of `parse_rule` for `whitespace_type` with `std::string` attribute, I do not understand how it happens.

---

## Comment 5

> Username: cppljevans  
> Created at: 2019-02-07 01:00:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/453#issuecomment-461250309  

On 2/6/19 6:47 PM, Nikita Kniazev wrote:  
> Hmm, something strange happens here, I will need more time to dig into this. `BOOST_SPIRIT_INSTANTIATE(grammar_type, iterator_type, context_type)` somehow leads to instantiation of `parse_rule` with `std::string` attribute, I do not understand how it happens.  
>   
  
I think because grammar_type::attribute_type **is** std::string and,  
according to what I see on my checkout of that commit, that's  
exactly what BOOST_SPIRIT_INSTANTIATE does:  
  
   #define BOOST_SPIRIT_INSTANTIATE(rule_type, Iterator, Context)   
  
     template bool parse_rule  
       < Iterator, Context,  
         rule_type::attribute_type //std::string  
       >  
       (...  
  
So where's the mystery?

---

## Comment 6

> Username: Kojoley  
> Created at: 2019-02-07 01:02:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/453#issuecomment-461250745  

I edited my message because I missed "for `whitespace_type`". Run the code or open GitHub to see it.

---

## Comment 7

> Username: Kojoley  
> Created at: 2019-02-07 01:10:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/453#issuecomment-461252374  

I think this happens because of ADL/overload resolution. I was thinking previously why `parse_rule` is not hidden under rule itself, looks like it was a useful idea.

---

## Comment 8

> Username: Xeverous  
> Created at: 2019-02-07 10:20:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/453#issuecomment-461362467  

> I tried the `const` workaround and it looks like it still works. Your code seems to uncover some other bug, will look into it.  
  
Yes, the workaround still works but now I have to place `const x3::unused_type` in more places. Code without any unused type in templates does not compile.  
  
Removing all `const x3::unused_type` and `x3::unused_type` does not solve the issue - try with the code I posted.

---

## Comment 9

> Username: Kojoley  
> Created at: 2019-02-08 12:17:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/453#issuecomment-461785133  

Thanks a ton for catching this!

---

## Comment 10

> Username: Xeverous  
> Created at: 2019-02-08 13:23:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/453#issuecomment-461801148  

Looks like I will be the bug hunter here. And all of this started accidentally when making a hobby parser project to automate some config generation tasks.  
  
The project isn't even at 30% completion and yet, every few commits I encounter weird compilation error or a runtime bug. Most of X3 bugs are catched by the compiler.  
  
I think you can expect more issues from me in the future, I haven't even started writing unit tests for my program.  
  
Thanks for the fast fix of this, I will test whether I can drop all of current X3 workarounds in my project.

---

## Comment 11

> Username: Kojoley  
> Created at: 2019-02-08 13:34:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/453#issuecomment-461804357  

> Looks like I will be the bug hunter here. ... I think you can expect more issues from me in the future  
  
That's fine. It is cool that you are using develop branch, I am trying not to break it, but the test suite cannot be perfect so I greatly appreciate any additional testing.

---

## Comment 12

> Username: Xeverous  
> Created at: 2019-02-08 13:40:42 UTC  
> Url: https://github.com/boostorg/spirit/issues/453#issuecomment-461806007  

I am using develop branch because the code from official Boost release does not compile. You can view my project as an additional library testing. I don't mind breaking changes. Nonetheless I'm surprised about some bugs ... I would not expect that noone has earlier tried non-copyable types in AST. Apparently there are very few users of X3 yet.

---

## Comment 13

> Username: Kojoley  
> Created at: 2019-02-08 13:56:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/453#issuecomment-461810537  

There were, but no one dig into what happen and it was not on my priority list. I spend more than 10 hours to sort out all the underlying problems and to make a good fix.  
  
It is just not a typical use case, with either forward_ast or unique/shared pointers it would not be a problem. I also received a green light from boost::variant maintainer for making recursive_wrapper more like forward_ast (but currently I spend time here =\).
