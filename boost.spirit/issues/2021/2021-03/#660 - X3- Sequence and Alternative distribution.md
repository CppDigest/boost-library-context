# #660 - X3: Sequence and Alternative distribution [Closed]

> Username: vehlwn  
> Created at: 2021-03-14 14:52:01 UTC  
> Updated at: 2021-05-17 17:17:51 UTC  
> Closed at: 2021-05-17 17:17:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/660  

Boost-1.75.0.  
Consider two equivalent calc4 examples:  
in spirit::qi: https://github.com/boostorg/spirit/blob/e0625d190bef75ce9be96b1963f153c3c9e65ada/example/qi/compiler_tutorial/calc4.cpp#L202  
in spirit::x3: https://github.com/boostorg/spirit/blob/e0625d190bef75ce9be96b1963f153c3c9e65ada/example/x3/calc/calc4.cpp#L187  
  
When I change in qi version:  
`expression = term >> *((char_('+') >> term) | (char_('-') >> term));`  
to  
`expression = term >> *((char_('+') | char_('-')) >> term);`  
it works.  
  
But when I do the same replacement in x3 version:  
`auto const expression_def = term >> *((char_('+') >> term) | (char_('-') >> term));`  
to  
`auto const expression_def = term >> *((char_('+') | char_('-')) >> term);`  
it fails to compile with the following errors:  
  
  
/usr/include/boost/spirit/home/x3/support/traits/move_to.hpp:196:24: **error**: no matching function for call to ‘move_to(const char&, client::ast::operation&, boost::mpl::identity<boost::spirit::x3::traits::tuple_attribute>::type)’  
196 |         detail::move_to(src, dest, typename attribute_category<Dest>::type());  
/usr/include/boost/spirit/home/x3/support/traits/move_to.hpp:56:9: note: candidate: ‘template<class Source, class Dest> void boost::spirit::x3::traits::detail::move_to(Source&, Dest&, boost::spirit::x3::traits::unused_attribute)’  
56 |         move_to(Source&, Dest&, unused_attribute) {}  
/usr/include/boost/spirit/home/x3/support/traits/move_to.hpp:56:9: note:   template argument deduction/substitution failed:  
/usr/include/boost/spirit/home/x3/support/traits/move_to.hpp:196:71: note:   cannot convert ‘boost::mpl::identity<boost::spirit::x3::traits::tuple_attribute>::type()’ (type ‘boost::mpl::identity<boost::spirit::x3::traits::tuple_attribute>::type’ {aka ‘boost::spirit::x3::traits::tuple_attribute’}) to type ‘boost::spirit::x3::traits::unused_attribute’  
196 |         detail::move_to(src, dest, typename attribute_category<Dest>::type());  
/usr/include/boost/spirit/home/x3/support/traits/move_to.hpp:74:9: note: candidate: ‘template<class Source, class Dest> void boost::spirit::x3::traits::detail::move_to(Source&, Dest&, boost::spirit::x3::traits::plain_attribute)’  
74 |         move_to(Source& src, Dest& dest, plain_attribute)  
...  
/usr/include/boost/spirit/home/x3/support/traits/move_to.hpp:97:9: **error**: no type named ‘type’ in ‘struct boost::enable_if<boost::mpl::and_<boost::spirit::x3::traits::is_same_size_sequence<client::ast::operation, const char>, boost::mpl::not_<boost::spirit::x3::traits::is_size_one_sequence<client::ast::operation> >, mpl_::bool_<true>, mpl_::bool_<true>, mpl_::bool_<true> >, void>’  
/usr/include/boost/spirit/home/x3/support/traits/move_to.hpp: In instantiation of ‘void boost::spirit::x3::traits::move_to(Source&&, Dest&) [with Source = const char&; Dest = client::ast::operation]’:  
/usr/include/boost/spirit/home/x3/char/char_parser.hpp:31:36:   required from ‘bool boost::spirit::x3::char_parser<Derived>::parse(Iterator&, const Iterator&, const Context&, boost::spirit::x3::unused_type, Attribute&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::ascii, boost::spirit::x3::space_tag>, boost::spirit::x3::unused_type>; Attribute = client::ast::operation; Derived = boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, char>]’  
...  
/usr/include/boost/fusion/algorithm/auxiliary/move.hpp:50:23: **error**: cannot convert ‘std::remove_reference<client::ast::operand&>::type’ {aka ‘client::ast::operand’} to ‘char’ in assignment  
50 |                 *dest = std::move(*src);  
  
I can attach a full compiler transcript if it's needed. Looks like a regression in x3 version.

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-03-14 17:21:00 UTC  
> Url: https://github.com/boostorg/spirit/issues/660#issuecomment-798944590  

The original `char_('+') >> term` has an attribute of `tuple<char, ast::program>` and it could be converted to `ast::operation` (`struct operation { char operator_; operand operand_; };`), the latter `char_('+')` has an attribute of `char` and there is no conversion from it to `ast::operation`. Qi is ok with that because of a https://svn.boost.org/trac10/ticket/8013 bug.  
  
```  
/usr/include/boost/fusion/algorithm/auxiliary/move.hpp:50:23: error: cannot convert ‘std::remove_referenceclient::ast::operand&::type’ {aka ‘client::ast::operand’} to ‘char’ in assignment  
50 | *dest = std::move(*src);  
```  
I do not know why it is reversed in your error message, for me Clang says:  
```  
boost/spirit/home/x3/support/traits/move_to.hpp:196:9: error: no matching function for call to 'move_to'  
        detail::move_to(src, dest, typename attribute_category<Dest>::type());  
        ^~~~~~~~~~~~~~~  
boost/spirit/home/x3/char/char_parser.hpp:31:29: note: in instantiation of function template  
      specialization 'boost::spirit::x3::traits::move_to<const char &, client::ast::operation>' requested here  
                x3::traits::move_to(*first, attr);  
                            ^  
...  
```  
  
If there were a static assert message, let's imagine saying `Cannot convert an attribute of a parser to an actual attribute`, would you figure out the issue in your parser yourself?

---

## Comment 2

> Username: vehlwn  
> Created at: 2021-03-16 15:27:37 UTC  
> Updated at: 2021-03-16 15:28:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/660#issuecomment-800362318  

How do I debug attribute collapsing process? I got these types from the compiler:  
```  
decltype(original_expression_def) == boost::spirit::x3::sequence  
<  
    boost::spirit::x3::rule<client::calculator_grammar::term, client::ast::program>,  
    boost::spirit::x3::kleene  
    <  
        boost::spirit::x3::alternative  
        <  
            boost::spirit::x3::sequence  
            <  
                boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, char>,  
                boost::spirit::x3::rule<client::calculator_grammar::term, client::ast::program>  
            >,  
            boost::spirit::x3::sequence  
            <  
                boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, char>,  
                boost::spirit::x3::rule<client::calculator_grammar::term, client::ast::program>  
            >  
        >  
    >  
>;  
  
decltype(my_expression_def) == boost::spirit::x3::sequence  
<  
    boost::spirit::x3::rule<client::calculator_grammar::term, client::ast::program>,  
    boost::spirit::x3::kleene  
    <  
        boost::spirit::x3::sequence  
        <  
            boost::spirit::x3::alternative  
            <  
                boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, char>,  
                boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, char>  
            >,  
            boost::spirit::x3::rule<client::calculator_grammar::term, client::ast::program>  
        >  
    >  
>  
```  
  
When I apply this rule for an Alternative: `a: A, b: A --> (a | b): A` - I get the same attribute for both original and my rule:  
```  
tuple  
<  
    program,  
    vector  
    <  
        tuple  
        <  
            char,  
            program  
        >  
    >  
>  
```  
Obviously my deduction is wrong because my example does not compile. What are the correct attributes for original and for my rule?

---

## Comment 3

> Username: Kojoley  
> Created at: 2021-03-16 16:07:33 UTC  
> Url: https://github.com/boostorg/spirit/issues/660#issuecomment-800395295  

In case of severe need in understanding a computed default attribute of a parser there is  
https://github.com/boostorg/spirit/blob/c5d0b5faf6693f798b4f6cbda749db93298a9ef4/include/boost/spirit/home/x3/support/traits/attribute_of.hpp#L16-L25, but for alternative parser it may give a wrong answer, see #610, it also have an example how to use the trait.
