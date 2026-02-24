# #152 X3: Fix expect directive's handling of container attributes [Merged]

> Username: vogelsgesang  
> Created at: 2015-08-19 22:28:07 UTC  
> Updated at: 2017-12-23 01:28:29 UTC  
> Merged at: 2017-12-23 00:12:39 UTC  
> Closed at: 2017-12-23 00:12:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/152  

So far, a kleene star within a expect directive did not correctly populate the attribute. For example the program  
  
``` cpp  
#include <iostream>  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/support/multi_pass.hpp>  
  
namespace x3 = boost::spirit::x3;  
namespace ascii = boost::spirit::x3::ascii;  
  
using x3::lit;  
using x3::char_;  
  
int main() {  
  auto const rule  
    = lit(':')  
      > *(char_('A', 'Z') | char_('a', 'z'))  
    ;  
  
  std::string input(":AaBbCcDd");  
  std::vector<char> result;  
  std::cout << "Success: " << parse(input.begin(), input.end(), rule, result) << std::endl;  
  for (auto& r : result) {  
    std::cout << r;  
  }  
  std::cout << std::endl;  
}  
```  
  
gives the following results:  
- the parser accepts the string, i.e. `parse` returns true  
- but the `result` vector only contains the last character of the string (`'d'`) instead of all the characters.  
  
This pull request fixes this misbehaviour, by adding the `handles_container` flag to the expect directive. It also contains a regression test.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2017-12-22 18:07:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/152#issuecomment-353648700  

A test in this PR works as it was fixed in 9e488859dacfacc2f1d87feb3d5aa2c11caede11 (it did not add a test, and fix in this PR would not solve that problem) by adding a specialization for expect directive for `parse_into_container` function.  
@djowel what do you think of the PR? I think it should be merged as it is logically correct and it also does add a test.

---

## Comment 2

> Username: djowel  
> Created_at: 2017-12-22 23:57:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/152#issuecomment-353692463  

Yes, please proceed.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2017-12-23 00:12:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/152#issuecomment-353693689  

Thanks @vogelsgesang!

---

## Comment 4

> Username: djowel  
> Created_at: 2017-12-23 00:23:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/152#issuecomment-353694647  

Seconded! Thanks @vogelsgesang!

---

## Comment 5

> Username: Kojoley  
> Created_at: 2017-12-23 00:47:22 UTC  
> Url: https://github.com/boostorg/spirit/pull/152#issuecomment-353696418  

Well, some how I have missed the problem locally with this PR:  
  
```  
expect.cpp  
.\boost/spirit/home/x3/core/detail/parse_into_container.hpp(299): error C2752: 'boost::spirit::x3::detail::parse_into_container_impl<Parser,Context,RContext,void>': more than one partial specialization matches the template argument list  
        with  
        [  
            Parser=boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>,  
            Context=boost::spirit::x3::context<boost::spirit::x3::skipper_tag,const boost::spirit::x3::ascii::space_type,boost::spirit::x3::unused_type>,  
            RContext=const boost::spirit::x3::unused_type  
        ]  
.\boost/spirit/home/x3/core/detail/parse_into_container.hpp(299): note: could be 'boost::spirit::x3::detail::parse_into_container_impl<Parser,Context,RContext,enable_if<boost::spirit::x3::traits::handles_container<Parser,Context,void>,void>::type>'  
.\boost/spirit/home/x3/core/detail/parse_into_container.hpp(299): note: or       'boost::spirit::x3::detail::parse_into_container_impl<boost::spirit::x3::expect_directive<Subject>,Context,RContext,void>'  
.\boost/spirit/home/x3/operator/detail/sequence.hpp(399): note: see reference to function template instantiation 'bool boost::spirit::x3::detail::parse_into_container<Right,Iterator,Context,RContext,Attribute>(const Parser &,Iterator &,const Iterator &,const Context &,RContext &,Attribute &)' being compiled  
        with  
        [  
            Right=boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>,  
            Iterator=const char *,  
            Context=boost::spirit::x3::context<boost::spirit::x3::skipper_tag,const boost::spirit::x3::ascii::space_type,boost::spirit::x3::unused_type>,  
            RContext=const boost::spirit::x3::unused_type,  
            Attribute=std::string,  
            Parser=boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>  
        ]  
.\boost/spirit/home/x3/operator/detail/sequence.hpp(484): note: see reference to function template instantiation 'bool boost::spirit::x3::detail::parse_sequence<boost::spirit::x3::sequence<boost::spirit::x3::extension::as_parser<char[2],void>::type,Derived>,Iterator,Context,RContext,Attribute>(const Parser &,Iterator &,const Iterator &,const Context &,RContext &,Attribute &,boost::spirit::x3::traits::container_attribute)' being compiled  
        with  
        [  
            Derived=boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>,  
            Iterator=const char *,  
            Context=boost::spirit::x3::context<boost::spirit::x3::skipper_tag,const boost::spirit::x3::ascii::space_type,boost::spirit::x3::unused_type>,  
            RContext=const boost::spirit::x3::unused_type,  
            Attribute=std::string,  
            Parser=boost::spirit::x3::sequence<boost::spirit::x3::extension::as_parser<char[2],void>::type,boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>>  
        ]  
.\boost/spirit/home/x3/operator/detail/sequence.hpp(517): note: see reference to function template instantiation 'bool boost::spirit::x3::detail::parse_into_container_impl<Parser,Context,RContext,void>::call<Iterator,Attribute>(const boost::spirit::x3::sequence<boost::spirit::x3::extension::as_parser<char[2],void>::type,Derived> &,Iterator &,const Iterator &,const Context &,RContext &,Attribute &,boost::mpl::false_)' being compiled  
        with  
        [  
            Parser=boost::spirit::x3::sequence<boost::spirit::x3::extension::as_parser<char[2],void>::type,boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>>,  
            Context=boost::spirit::x3::context<boost::spirit::x3::skipper_tag,const boost::spirit::x3::ascii::space_type,boost::spirit::x3::unused_type>,  
            RContext=const boost::spirit::x3::unused_type,  
            Iterator=const char *,  
            Attribute=std::string,  
            Derived=boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>  
        ]  
.\boost/spirit/home/x3/operator/detail/sequence.hpp(516): note: see reference to function template instantiation 'bool boost::spirit::x3::detail::parse_into_container_impl<Parser,Context,RContext,void>::call<Iterator,Attribute>(const boost::spirit::x3::sequence<boost::spirit::x3::extension::as_parser<char[2],void>::type,Derived> &,Iterator &,const Iterator &,const Context &,RContext &,Attribute &,boost::mpl::false_)' being compiled  
        with  
        [  
            Parser=boost::spirit::x3::sequence<boost::spirit::x3::extension::as_parser<char[2],void>::type,boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>>,  
            Context=boost::spirit::x3::context<boost::spirit::x3::skipper_tag,const boost::spirit::x3::ascii::space_type,boost::spirit::x3::unused_type>,  
            RContext=const boost::spirit::x3::unused_type,  
            Iterator=const char *,  
            Attribute=std::string,  
            Derived=boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>  
        ]  
.\boost/spirit/home/x3/core/detail/parse_into_container.hpp(300): note: see reference to function template instantiation 'bool boost::spirit::x3::detail::parse_into_container_impl<Parser,Context,RContext,void>::call<Iterator,Attribute>(const boost::spirit::x3::sequence<boost::spirit::x3::extension::as_parser<char[2],void>::type,Derived> &,Iterator &,const Iterator &,const Context &,RContext &,Attribute &)' being compiled  
        with  
        [  
            Parser=boost::spirit::x3::sequence<boost::spirit::x3::extension::as_parser<char[2],void>::type,boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>>,  
            Context=boost::spirit::x3::context<boost::spirit::x3::skipper_tag,const boost::spirit::x3::ascii::space_type,boost::spirit::x3::unused_type>,  
            RContext=const boost::spirit::x3::unused_type,  
            Iterator=const char *,  
            Attribute=std::string,  
            Derived=boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>  
        ]  
.\boost/spirit/home/x3/core/detail/parse_into_container.hpp(299): note: see reference to function template instantiation 'bool boost::spirit::x3::detail::parse_into_container_impl<Parser,Context,RContext,void>::call<Iterator,Attribute>(const boost::spirit::x3::sequence<boost::spirit::x3::extension::as_parser<char[2],void>::type,Derived> &,Iterator &,const Iterator &,const Context &,RContext &,Attribute &)' being compiled  
        with  
        [  
            Parser=boost::spirit::x3::sequence<boost::spirit::x3::extension::as_parser<char[2],void>::type,boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>>,  
            Context=boost::spirit::x3::context<boost::spirit::x3::skipper_tag,const boost::spirit::x3::ascii::space_type,boost::spirit::x3::unused_type>,  
            RContext=const boost::spirit::x3::unused_type,  
            Iterator=const char *,  
            Attribute=std::string,  
            Derived=boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>  
        ]  
.\boost/spirit/home/x3/operator/detail/sequence.hpp(398): note: see reference to function template instantiation 'bool boost::spirit::x3::detail::parse_into_container<Left,Iterator,Context,RContext,Attribute>(const Parser &,Iterator &,const Iterator &,const Context &,RContext &,Attribute &)' being compiled  
        with  
        [  
            Left=boost::spirit::x3::sequence<boost::spirit::x3::extension::as_parser<char[2],void>::type,boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>>,  
            Iterator=const char *,  
            Context=boost::spirit::x3::context<boost::spirit::x3::skipper_tag,const boost::spirit::x3::ascii::space_type,boost::spirit::x3::unused_type>,  
            RContext=const boost::spirit::x3::unused_type,  
            Attribute=std::string,  
            Parser=boost::spirit::x3::sequence<boost::spirit::x3::extension::as_parser<char[2],void>::type,boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>>  
        ]  
.\boost/spirit/home/x3/operator/sequence.hpp(45): note: see reference to function template instantiation 'bool boost::spirit::x3::detail::parse_sequence<boost::spirit::x3::sequence<Derived,boost::spirit::x3::expect_directive<boost::spirit::x3::extension::as_parser<char,void>::type>>,Iterator,Context,RContext,Attribute>(const Parser &,Iterator &,const Iterator &,const Context &,RContext &,Attribute &,boost::spirit::x3::traits::container_attribute)' being compiled  
        with  
        [  
            Derived=boost::spirit::x3::sequence<boost::spirit::x3::extension::as_parser<char[2],void>::type,boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>>,  
            Iterator=const char *,  
            Context=boost::spirit::x3::context<boost::spirit::x3::skipper_tag,const boost::spirit::x3::ascii::space_type,boost::spirit::x3::unused_type>,  
            RContext=const boost::spirit::x3::unused_type,  
            Attribute=std::string,  
            Parser=boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::extension::as_parser<char[2],void>::type,boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>>,boost::spirit::x3::expect_directive<boost::spirit::x3::extension::as_parser<char,void>::type>>  
        ]  
.\boost/spirit/home/x3/core/parse.hpp(119): note: see reference to function template instantiation 'bool boost::spirit::x3::sequence<Derived,boost::spirit::x3::expect_directive<boost::spirit::x3::extension::as_parser<char,void>::type>>::parse<Iterator,boost::spirit::x3::context<boost::spirit::x3::skipper_tag,const boost::spirit::x3::char_class<boost::spirit::char_encoding::ascii,boost::spirit::x3::space_tag>,boost::spirit::x3::unused_type>,const boost::spirit::x3::unused_type,Attribute>(Iterator &,const Iterator &,const Context &,RContext &,Attribute &) const' being compiled  
        with  
        [  
            Derived=boost::spirit::x3::sequence<boost::spirit::x3::extension::as_parser<char[2],void>::type,boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>>,  
            Iterator=const char *,  
            Attribute=std::string,  
            Context=boost::spirit::x3::context<boost::spirit::x3::skipper_tag,const boost::spirit::x3::ascii::space_type,boost::spirit::x3::unused_type>,  
            RContext=const boost::spirit::x3::unused_type  
        ]  
.\boost/spirit/home/x3/core/parse.hpp(119): note: see reference to function template instantiation 'bool boost::spirit::x3::sequence<Derived,boost::spirit::x3::expect_directive<boost::spirit::x3::extension::as_parser<char,void>::type>>::parse<Iterator,boost::spirit::x3::context<boost::spirit::x3::skipper_tag,const boost::spirit::x3::char_class<boost::spirit::char_encoding::ascii,boost::spirit::x3::space_tag>,boost::spirit::x3::unused_type>,const boost::spirit::x3::unused_type,Attribute>(Iterator &,const Iterator &,const Context &,RContext &,Attribute &) const' being compiled  
        with  
        [  
            Derived=boost::spirit::x3::sequence<boost::spirit::x3::extension::as_parser<char[2],void>::type,boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>>,  
            Iterator=const char *,  
            Attribute=std::string,  
            Context=boost::spirit::x3::context<boost::spirit::x3::skipper_tag,const boost::spirit::x3::ascii::space_type,boost::spirit::x3::unused_type>,  
            RContext=const boost::spirit::x3::unused_type  
        ]  
.\boost/spirit/home/x3/core/parse.hpp(136): note: see reference to function template instantiation 'bool boost::spirit::x3::phrase_parse_main<Iterator,Parser,Skipper,Attribute>(Iterator &,Iterator,const Parser &,const Skipper &,Attribute &,boost::spirit::x3::skip_flag)' being compiled  
        with  
        [  
            Iterator=const char *,  
            Parser=boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::extension::as_parser<char[2],void>::type,boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>>,boost::spirit::x3::expect_directive<boost::spirit::x3::extension::as_parser<char,void>::type>>,  
            Skipper=boost::spirit::x3::ascii::space_type,  
            Attribute=std::string  
        ]  
c:\projects\boost\libs\spirit\test\x3\test.hpp(65): note: see reference to function template instantiation 'bool boost::spirit::x3::phrase_parse<const Char*,Parser,Skipper,Attr>(Iterator &,Iterator,const Parser &,const Skipper &,Attribute &,boost::spirit::x3::skip_flag)' being compiled  
        with  
        [  
            Char=char,  
            Parser=boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::extension::as_parser<char[2],void>::type,boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>>,boost::spirit::x3::expect_directive<boost::spirit::x3::extension::as_parser<char,void>::type>>,  
            Skipper=boost::spirit::x3::ascii::space_type,  
            Attr=std::string,  
            Iterator=const char *,  
            Attribute=std::string  
        ]  
libs\spirit\test\x3\expect.cpp(112): note: see reference to function template instantiation 'bool spirit_test::test_attr<char,boost::spirit::x3::sequence<Derived,boost::spirit::x3::expect_directive<boost::spirit::x3::extension::as_parser<char,void>::type>>,std::string,boost::spirit::x3::ascii::space_type>(const Char *,const Parser &,Attr &,const Skipper &,bool)' being compiled  
        with  
        [  
            Derived=boost::spirit::x3::sequence<boost::spirit::x3::extension::as_parser<char[2],void>::type,boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>>,  
            Char=char,  
            Parser=boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::extension::as_parser<char[2],void>::type,boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::alternative<boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>,boost::spirit::x3::literal_char<boost::spirit::char_encoding::ascii,boost::spirit::char_encoding::ascii::char_type>>>>>,boost::spirit::x3::expect_directive<boost::spirit::x3::extension::as_parser<char,void>::type>>,  
            Attr=std::string,  
            Skipper=boost::spirit::x3::ascii::space_type  
        ]  
```  
  
Reverted in 681db787d844b3908f49d34ef3fc0b979d02f535.

---

## Comment 6

> Username: vogelsgesang  
> Created_at: 2017-12-23 01:28:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/152#issuecomment-353698875  

Thanks for considering this PR and sorry that it broke the test cases.  
I can't claim to remember if they were successful back in 2015, but I would guess that my changes conflict with 9e48885.  
Well, it seems that at least the test case survived, so the functionality I wanted to fix when I created this PR seems to be still working. I assume this was also fixed by 9e48885.  
  
Nice to see that there is active development on Spirit again!

---
