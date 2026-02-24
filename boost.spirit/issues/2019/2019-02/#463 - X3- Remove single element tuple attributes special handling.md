# #463 - X3: Remove single element tuple attributes special handling [Open]

> Username: Kojoley  
> Created at: 2019-02-14 01:47:46 UTC  
> Updated at: 2025-05-10 01:46:43 UTC  
> Url: https://github.com/boostorg/spirit/issues/463  

The question about single element tuple attributes arises from time to time. We have some basic support for unwrapping them and because of that users use them and find that more complex situation are not supported.  
  
Usually I think that what's logical for users that should be supported and previously I implemented some logic to improve the handling, but with time I realized that it the basics of this usage is ill-formed, because such attributes are simultaneously could be of plain, container and Fusion sequences types.  
  
There also no benefits in using them, most frequently the question arises in term of adapted Fusion structs:  
```cpp  
struct number {  
    double value;  
};  
BOOST_FUSION_ADAPT_STRUCT(number, value)  
```  
or  
```cpp  
struct ints_ {  
    std::vector<int> m_vector;  
};  
BOOST_FUSION_ADAPT_STRUCT(ints_, m_vector)  
```  
  
There is no reason to use them instead of:  
```cpp  
struct number {  
    double value;  
    number& operator=(double x) { value = x; return *this; }  
};  
  
struct ints_ : std::vector<int> {};  
```  
  
No Fusion, supported already, clear logic. If someone do not like inheritance in the case above they could provide STL container API for their class, or specialize the traits.  
  
By removing special handling of single element sequence I mean removing of currently supported:  
```cpp  
        boost::fusion::vector<int> attr;  
        BOOST_TEST(test_attr("123456", int_, attr));  
  
        boost::fusion::vector<std::string> attr;  
        BOOST_TEST(test_attr("abcdef", *alnum, attr));  
```  
It will allow to place a static assertion in `parse_into_container` that expects `traits::is_container` to be true for the attribute.  
  
Refs: #178 #408 #461

---

## Comment 1

> Username: Xeverous  
> Created at: 2019-02-14 12:31:38 UTC  
> Url: https://github.com/boostorg/spirit/issues/463#issuecomment-463610331  

Would such type  
  
```cpp  
struct number {  
    double value;  
    number& operator=(double x) { value = x; return *this; }  
};  
```  
  
work when inherited from `x3::position_tagged`?

---

## Comment 2

> Username: Kojoley  
> Created at: 2019-02-14 12:37:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/463#issuecomment-463611901  

It should. Why not to try yourself before asking?

---

## Comment 3

> Username: Xeverous  
> Created at: 2019-02-14 12:39:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/463#issuecomment-463612581  

In case of potential bugs - recently I have experienced enough bugs with Spirit that I better verify whether what I do is correct use of the library or not (to confirm whether the error comes from my misuse or from library bug).

---

## Comment 4

> Username: Xeverous  
> Created at: 2019-02-16 23:56:42 UTC  
> Url: https://github.com/boostorg/spirit/issues/463#issuecomment-464401034  

What do you think about adding static assert for 1-element fusions?

---

## Comment 5

> Username: Kojoley  
> Created at: 2019-02-17 00:39:35 UTC  
> Url: https://github.com/boostorg/spirit/issues/463#issuecomment-464403207  

> What do you think about adding static assert for 1-element fusions?  
  
I do not know what you mean by that. Quoting myself (from the first post):  
  
> removing special handling of single element sequence ... will allow to place a static assertion in `parse_into_container` that expects `traits::is_container` to be true for the attribute.

---

## Comment 6

> Username: Xeverous  
> Created at: 2019-02-17 15:29:26 UTC  
> Updated at: 2019-02-17 15:29:33 UTC  
> Url: https://github.com/boostorg/spirit/issues/463#issuecomment-464470577  

I mean placing a static assert that checks if a rule is instantiated using 1-element fusion. If so, make a compile error and mention to overload `operator=` instead.

---

## Comment 7

> Username: Kojoley  
> Created at: 2019-02-17 15:42:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/463#issuecomment-464471979  

The rule should not do such a thing. Moreover, you can add a custom parser that will handle any type you want, `attr` 'parser' is in this category already.

---

## Comment 8

> Username: Xeverous  
> Created at: 2019-02-17 17:21:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/463#issuecomment-464483765  

Then I propose a tutorial page in the doc that will explain how to write a custom parser (some example boilerplate code and where to put what)

---

## Comment 9

> Username: saki7  
> Created at: 2020-03-06 16:41:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/463#issuecomment-595853974  

I'm with @Xeverous on this. It would be really nice to have a clear error message; the current error   
output for this (wrong) usage of single element fusion sequence is confusing:  
  
```  
In file included from prog.cc:1:  
In file included from /opt/wandbox/boost-1.72.0/clang-head/include/boost/spirit/home/x3.hpp:14:  
In file included from /opt/wandbox/boost-1.72.0/clang-head/include/boost/spirit/home/x3/auxiliary.hpp:11:  
In file included from /opt/wandbox/boost-1.72.0/clang-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17:  
/opt/wandbox/boost-1.72.0/clang-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:113:18: error: no viable overloaded '='  
            dest = std::move(src);  
            ~~~~ ^ ~~~~~~~~~~~~~~  
/opt/wandbox/boost-1.72.0/clang-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:150:13: note: in instantiation of function template specialization 'boost::spirit::x3::traits::detail::move_to<int &, boost::variant<Int, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > > >' requested here  
            move_to(src, dest, tag, is_size_one_sequence<Source>());  
            ^  
/opt/wandbox/boost-1.72.0/clang-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:201:17: note: in instantiation of function template specialization 'boost::spirit::x3::traits::detail::move_to<int, boost::variant<Int, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > > >' requested here  
        detail::move_to(std::move(src), dest  
                ^  
/opt/wandbox/boost-1.72.0/clang-head/include/boost/spirit/home/x3/operator/detail/alternative.hpp:239:21: note: in instantiation of function template specialization 'boost::spirit::x3::traits::move_to<int &, boost::variant<Int, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > > >' requested here  
            traits::move_to(attr_, attr);  
                    ^  
/opt/wandbox/boost-1.72.0/clang-head/include/boost/spirit/home/x3/operator/detail/alternative.hpp:255:69: note: in instantiation of function template specialization 'boost::spirit::x3::detail::move_if_not_alternative<mpl_::bool_<false> >::call<int, boost::variant<Int, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > > >' requested here  
            move_if_not_alternative<typename pass::is_alternative>::call(attr_, attr);  
                                                                    ^  
/opt/wandbox/boost-1.72.0/clang-head/include/boost/spirit/home/x3/operator/alternative.hpp:39:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::parse_alternative<boost::spirit::x3::int_parser<int, 10, 1, -1>, std::__1::__wrap_iter<const char *>, boost::spirit::x3::unused_type, const boost::spirit::x3::unused_type, boost::variant<Int, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > > >' requested here  
            return detail::parse_alternative(this->left, first, last, context, rcontext, attr)  
                           ^  
/opt/wandbox/boost-1.72.0/clang-head/include/boost/spirit/home/x3/core/parse.hpp:36:29: note: in instantiation of function template specialization 'boost::spirit::x3::alternative<boost::spirit::x3::int_parser<int, 10, 1, -1>, boost::spirit::x3::plus<boost::spirit::x3::any_char<boost::spirit::char_encoding::standard> > >::parse<std::__1::__wrap_iter<const char *>, boost::spirit::x3::unused_type, const boost::spirit::x3::unused_type, boost::variant<Int, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > > >' requested here  
        return as_parser(p).parse(first, last, unused, unused, attr);  
                            ^  
/opt/wandbox/boost-1.72.0/clang-head/include/boost/spirit/home/x3/core/parse.hpp:61:16: note: in instantiation of function template specialization 'boost::spirit::x3::parse_main<std::__1::__wrap_iter<const char *>, boost::spirit::x3::alternative<boost::spirit::x3::int_parser<int, 10, 1, -1>, boost::spirit::x3::plus<boost::spirit::x3::any_char<boost::spirit::char_encoding::standard> > >, boost::variant<Int, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > > >' requested here  
        return parse_main(first, last, p, attr);  
               ^  
prog.cc:28:13: note: in instantiation of function template specialization 'boost::spirit::x3::parse<std::__1::__wrap_iter<const char *>, boost::spirit::x3::alternative<boost::spirit::x3::int_parser<int, 10, 1, -1>, boost::spirit::x3::plus<boost::spirit::x3::any_char<boost::spirit::char_encoding::standard> > >, boost::variant<Int, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > > >' requested here  
    if (x3::parse(std::begin(input), std::end(input), rule, attr)) {  
            ^  
/opt/wandbox/boost-1.72.0/clang-head/include/boost/variant/variant.hpp:2134:9: note: candidate template ignored: disabled by 'enable_if' [with T = int]  
        boost::mpl::and_<  
        ^  
/opt/wandbox/boost-1.72.0/clang-head/include/boost/variant/variant.hpp:2149:9: note: candidate template ignored: disabled by 'enable_if' [with T = int]  
        mpl::or_<  
        ^  
/opt/wandbox/boost-1.72.0/clang-head/include/boost/variant/variant.hpp:2161:14: note: candidate function not viable: no known conversion from 'typename remove_reference<int &>::type' (aka 'int') to 'const boost::variant<Int, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > >' for 1st argument  
    variant& operator=(const variant& rhs)  
             ^  
/opt/wandbox/boost-1.72.0/clang-head/include/boost/variant/variant.hpp:2168:14: note: candidate function not viable: no known conversion from 'typename remove_reference<int &>::type' (aka 'int') to 'boost::variant<Int, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > >' for 1st argument  
    variant& operator=(variant&& rhs)   
             ^  
1 error generated.  
```  
(code from: https://github.com/boostorg/spirit/issues/574#issuecomment-595598158)  
  
If it's going to be treated as an error anyway, why not show a simple message by static_assert?

---

## Comment 10

> Username: Kojoley  
> Created at: 2020-03-06 16:48:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/463#issuecomment-595857422  

> If it's going to be treated as an error anyway, why not show a simple message by static_assert?  
  
I would love to get a PR :-)  
The last time I tried to make it working I go hit by sequence collapsing magic, maybe you can sort it out.

---

## Comment 11

> Username: Xeverous  
> Created at: 2020-03-06 18:20:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/463#issuecomment-595895969  

Assert straight at the beginning? Or "the beginning (function)" is already reused by recursion which causes problems in cases assert should not fire?  
  
Btw I think X3 should document some recommendations for making AST types in its tutorial as what I'm currently following is a result of trial-and-error:  
  
When making an AST type, I'm always going into 1 of these:  
  
- AST type of 1 member `T`: overload `operator=(T)`  
- AST type of 2+ members: `BOOST_FUSION_ADAPT_STRUCT`  
- AST type that inherits from some container (no further code required but must not contain members)  
- AST type that inherits from `x3::variant`: add `using base_type::base_type;`, `using base_type::operator=`, must not contain members  
  
Other mixes of AST types end in various (long long) compilation errors.

---

## Comment 12

> Username: saki7  
> Created at: 2020-03-06 18:28:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/463#issuecomment-595899441  

@Xeverous your examples are really useful IMHO. I wish I had known that three years ago. I personally have never inherited from `x3::variant`; I'm interested in the details.

---

## Comment 13

> Username: Xeverous  
> Created at: 2020-03-06 21:50:28 UTC  
> Url: https://github.com/boostorg/spirit/issues/463#issuecomment-595980045  

I actually don't remember who/where told me about inheriting from containers and `x3::variant`, but IIRC it was somewhere here on a different issue. All this stuff is an effect of X3 implementation details that detect what an AST type is like. IMO such recommendations should be in the documentation.  
  
My X3-based project has a lot of grammars (60+) so you might want to check https://github.com/Xeverous/filter_spirit/tree/develop/src/lib/fs/parser how exactly I use X3.

---

## Comment 14

> Username: Kojoley  
> Created at: 2020-03-06 22:52:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/463#issuecomment-595998603  

> I actually don't remember who/where told me about inheriting from containers and x3::variant, but IIRC it was somewhere here on a different issue. All this stuff is an effect of X3 implementation details that detect what an AST type is like. IMO such recommendations should be in the documentation.  
  
You are very welcome to propose improvements to documentation via opening a pull request. The problem is that people do not read the documentation  
https://www.boost.org/doc/libs/1_72_0/libs/spirit/doc/x3/html/spirit_x3/tutorials/rexpr.html#spirit_x3.tutorials.rexpr.the_ast

---

## Comment 15

> Username: Xeverous  
> Created at: 2020-03-06 23:06:33 UTC  
> Url: https://github.com/boostorg/spirit/issues/463#issuecomment-596002223  

The variant example is mentioned but:  
  
- (at least at the moment I was reading doc some time ago) - no mentions that 1-element fusion sequences are not supported  
- no mention that X3 actually changes behavior depending on what `operator=` accepts  
- There could be some general recommendation for AST type - so far I know it recommends small incremental changes to the grammar but I realized that the combo of container-ast/variant-ast/fusion-ast/1-element-operator=-ast is the easiest to work with.

---

## Comment 16

> Username: Kojoley  
> Created at: 2020-03-07 00:25:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/463#issuecomment-596018980  

> The variant example is mentioned  
  
I am sorry, but you did not mention it and said just above that the documentation should have it.  
  
> (at least at the moment I was reading doc some time ago) - no mentions that 1-element fusion sequences are not supported  
  
Fusion sequences are supported as **sequences** for sequence parser and as values for any other parser.  
  
It just happens that sequence parser is [documented](https://www.boost.org/doc/libs/1_72_0/libs/spirit/doc/x3/html/spirit_x3/quick_reference/compound_attribute_rules.html) as  
```  
a: A, b: B --> (a >> b): tuple<A, B>  
a: A, b: Unused --> (a >> b): A  
a: Unused, b: B --> (a >> b): B  
a: Unused, b: Unused --> (a >> b): Unused  
  
a: A, b: A --> (a >> b): vector<A>  
a: vector<A>, b: A --> (a >> b): vector<A>  
a: A, b: vector<A> --> (a >> b): vector<A>  
a: vector<A>, b: vector<A> --> (a >> b): vector<A>  
```  
  
to not produce single element tuple and there is no (or prove me wrong) mention that value parser will treat `tuple<A>` as `A`.  
  
> no mention that X3 actually changes behavior depending on what `operator=` accepts  
  
I am not sure I understand you. Spirit (non-container/variant value parser) uses `operator=` to assign a value to an attribute because it uses output parameter as the only logical way to update the value (the other design could use return value and would have been assigning via constructing the value in place). It could check whether `operator=` is available and try to use other ways but not currently. Could you explain what you meant?   
  
> There could be some general recommendation for AST type ...  
  
There are many ways to design your AST, what is working for you may not work for others.   
  
> ... - so far I know it recommends small incremental changes to the grammar but I realized that the combo of container-ast/variant-ast/fusion-ast/1-element-operator=-ast is the easiest to work with.  
  
You can share your experience via a blog post or documentation update pull request.
