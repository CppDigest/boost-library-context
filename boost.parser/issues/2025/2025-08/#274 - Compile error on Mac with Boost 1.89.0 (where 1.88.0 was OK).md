# #274 - Compile error on Mac with Boost 1.89.0 (where 1.88.0 was OK) [Closed]

> Username: jwwalker  
> Created at: 2025-08-28 17:18:54 UTC  
> Updated at: 2025-09-02 21:52:15 UTC  
> Closed at: 2025-09-02 21:52:15 UTC  
> Url: https://github.com/boostorg/parser/issues/274  

Compiling this code:  
  
```c++  
#pragma clang diagnostic push  
#pragma clang diagnostic ignored "-Wdocumentation"  
#import <boost/parser/parser.hpp>  
#import <boost/parser/transcode_view.hpp>	// needed for as_utf32  
#pragma clang diagnostic pop  
  
#import <string>  
  
namespace bp = ::boost::parser;  
  
bp::rule< struct factor, double > factor = "factor";  
bp::rule< struct assignment > assignment = "assignment";  
  
auto const factor_def = bp::lit("X") > factor;  
  
auto const assignment_def = factor;  
  
BOOST_PARSER_DEFINE_RULES( assignment, factor );  
  
static void	Calc( const std::string& inText )  
{  
	bp::parse( inText | bp::as_utf32, assignment, bp::ws );  
}  
```  
  
Clang shows this error:  
  
```  
/Volumes/Work/mercurial/boost_1_89_0/boost/parser/parser.hpp:583:34: error: assigning to 'nope_or_pointer_t<double>' from incompatible type 'const nope_or_pointer_t<boost::parser::detail::nope>'  
                    val_ = other.val_;  
                           ~~~~~~^~~~  
/Volumes/Work/mercurial/boost_1_89_0/boost/parser/parser.hpp:735:20: note: in instantiation of function template specialization 'boost::parser::detail::parse_context<false, false, boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::default_error_handler, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, double, factor>::parse_context<boost::parser::detail::nope, factor, boost::parser::detail::nope, boost::parser::detail::nope, double, factor, boost::parser::detail::nope, boost::parser::detail::nope>' requested here  
            return result_type(context, tag_ptr, value, locals, params);  
                   ^  
/Volumes/Work/mercurial/boost_1_89_0/boost/parser/parser.hpp:3745:31: note: in instantiation of function template specialization 'boost::parser::rule_parser<false, factor, double, boost::parser::detail::nope, boost::parser::detail::nope>::call<boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::detail::parse_context<false, false, boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::default_error_handler, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, factor>, boost::parser::detail::null_parser>' requested here  
                return parser.call(  
                              ^  
/Volumes/Work/mercurial/boost_1_89_0/boost/parser/parser.hpp:5424:17: note: in instantiation of function template specialization 'parse_rule<boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::detail::parse_context<false, false, boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::default_error_handler, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, factor>, boost::parser::detail::null_parser, boost::parser::detail::nope>' requested here  
                parse_rule(  
                ^  
/Volumes/Work/mercurial/boost_1_89_0/boost/parser/parser.hpp:5824:21: note: in instantiation of function template specialization 'boost::parser::rule_parser<false, factor, double, boost::parser::detail::nope, boost::parser::detail::nope>::call<boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::detail::parse_context<false, false, boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::default_error_handler, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, assignment>, boost::parser::detail::null_parser, boost::parser::detail::nope>' requested here  
            parser_.call(first, last, context, skip, flags, success, attr);  
                    ^  
/Volumes/Work/Users/jwwalker/Projects/ParserPlay/PlainCalc3/PlainCalc3/Calculator Core/Calculate.cpp:18:1: note: in instantiation of function template specialization 'boost::parser::parser_interface<boost::parser::rule_parser<false, factor, double, boost::parser::detail::nope, boost::parser::detail::nope>>::operator()<boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::detail::parse_context<false, false, boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::default_error_handler, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, assignment>, boost::parser::detail::null_parser, boost::parser::detail::nope>' requested here  
BOOST_PARSER_DEFINE_RULES( assignment, factor );  
^  
In file included from /Volumes/Work/Users/jwwalker/Projects/ParserPlay/PlainCalc3/PlainCalc3/Calculator Core/Calculate.cpp:3:  
/Volumes/Work/mercurial/boost_1_89_0/boost/parser/parser.hpp:6194:30: note: expanded from macro 'BOOST_PARSER_DEFINE_RULES'  
    BOOST_PARSER_PP_FOR_EACH(BOOST_PARSER_DEFINE_IMPL, _, __VA_ARGS__)  
                             ^  
/Volumes/Work/mercurial/boost_1_89_0/boost/parser/parser.hpp:5342:29: note: in instantiation of function template specialization 'parse_rule<boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::detail::parse_context<false, false, boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::default_error_handler, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, assignment>, boost::parser::detail::null_parser>' requested here  
                auto attr = parse_rule(  
                            ^  
/Volumes/Work/mercurial/boost_1_89_0/boost/parser/parser.hpp:5804:28: note: in instantiation of function template specialization 'boost::parser::rule_parser<false, assignment, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>::call<boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::detail::parse_context<false, false, boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8, boost::parser::detail::text::format::utf32, std::__wrap_iter<const char *>>, boost::parser::default_error_handler>, boost::parser::detail::null_parser>' requested here  
            return parser_.call(first, last, context, skip, flags, success);  
```  
  
My code was compiling OK with Boost 1.88.0.

---

## Comment 1

> Username: jwwalker  
> Created at: 2025-09-02 21:52:15 UTC  
> Url: https://github.com/boostorg/parser/issues/274#issuecomment-3246913534  

OK, I figured it out.  Apparently, what the error message was trying to tell me was that there was an incompatibility between the attributes produced by my rules.  It can be fixed by setting the attribute of `assignment ` to `double`, or by setting `assignment_def = bp::omit[ factor ]`.
