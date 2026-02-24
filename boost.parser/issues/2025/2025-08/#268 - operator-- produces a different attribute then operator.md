# #268 - operator|| produces a different attribute then operator>> [Closed]

> Username: IBims1NicerTobi  
> Created at: 2025-08-12 02:11:04 UTC  
> Updated at: 2025-10-31 17:41:00 UTC  
> Closed at: 2025-10-31 17:41:00 UTC  
> Url: https://github.com/boostorg/parser/issues/268  

```cpp  
constexpr bp::rule<struct name, std::string_view> name = "name";  
auto name_def = bp::string_view[bp::lexeme[+(bp::lower | bp::upper | bp::digit | bp::char_("_"))]];  
BOOST_PARSER_DEFINE_RULES(name)  
constexpr bp::rule<struct qd_vec, std::vector<double>> qd_vec = "qd_vec";  
auto qd_vec_def = bp::lit("\"") >> bp::double_ % (bp::lit(",") | (bp::lit("\"") >> bp::lit(",") >> bp::lit("\""))) >> bp::lit('\"');  
BOOST_PARSER_DEFINE_RULES(qd_vec)  
struct lu_table_template_1  
{  
  std::vector<double> index_1;  
  std::string_view variable_1;  
};  
constexpr boost::parser::rule<struct lu_table_template_1_tag, lu_table_template_1> lu_table_template_1_rule = "lu_table_template_1";  
auto lu_table_template_1_rule_def = (bp::lit("index_1") >> '(' >> qd_vec >> ')' >> ';') >> (bp::lit("variable_1") >> ':' >> name >> ';');  
BOOST_PARSER_DEFINE_RULES(lu_table_template_1_rule)  
```  
The code above works perfectly fine. However if we change the `>>` to a `||` like this:  
```cpp  
auto lu_table_template_1_rule_def = (bp::lit("index_1") >> '(' >> qd_vec >> ')' >> ';') || (bp::lit("variable_1") >> ':' >> name >> ';');  
```  
the code fails to compile even though (to my understanding) the attributes of `>>` and `||` should be exactly the same. This is the resulting error:  
```cpp  
/scratch_raid/tobitom/ester/build/_deps/boost-src/libs/parser/include/boost/parser/parser.hpp:3552:17: error: no matching member function for call to 'call_impl'  
 3552 |                 call_impl(  
      |                 ^~~~~~~~~  
/scratch_raid/tobitom/ester/build/_deps/boost-src/libs/parser/include/boost/parser/parser.hpp:5772:21: note: in instantiation of function template specialization 'boost::parser::perm_parser<std::tuple<boost::parser::seq_parser<std::tuple<boost::parser::omit_parser<boost::parser::string_parser<const char *, boost::parser::detail::text::null_sentinel_t>>, boost::parser::omit_parser<boost::parser::char_parser<char>>, boost::parser::rule_parser<false, ester::circuit::library_parser::qd_vec, std::vector<double>, boost::parser::detail::nope, boost::parser::detail::nope>, boost::parser::omit_parser<boost::parser::char_parser<char>>, boost::parser::omit_parser<boost::parser::char_parser<char>>>, std::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>>, std::tuple<std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>>>, boost::parser::seq_parser<std::tuple<boost::parser::omit_parser<boost::parser::string_parser<const char *, boost::parser::detail::text::null_sentinel_t>>, boost::parser::omit_parser<boost::parser::char_parser<char>>, boost::parser::rule_parser<false, ester::circuit::library_parser::name, std::basic_string_view<char>, boost::parser::detail::nope, boost::parser::detail::nope>, boost::parser::omit_parser<boost::parser::char_parser<char>>>, std::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>>, std::tuple<std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>>>>, boost::parser::detail::nope>::call<const char *, const char *, boost::parser::detail::parse_context<false, false, const char *, const char *, boost::parser::default_error_handler, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, ester::circuit::library_parser::lu_table_template_1, ester::circuit::library_parser::lu_table_template_1_tag>, boost::parser::detail::null_parser, ester::circuit::library_parser::lu_table_template_1>' requested here  
 5772 |             parser_.call(first, last, context, skip, flags, success, attr);  
      |                     ^  
/scratch_raid/tobitom/ester/src/circuit/include/ester/circuit/library_parser.hpp:237:1: note: in instantiation of function template specialization 'boost::parser::parser_interface<boost::parser::perm_parser<std::tuple<boost::parser::seq_parser<std::tuple<boost::parser::omit_parser<boost::parser::string_parser<const char *, boost::parser::detail::text::null_sentinel_t>>, boost::parser::omit_parser<boost::parser::char_parser<char>>, boost::parser::rule_parser<false, ester::circuit::library_parser::qd_vec, std::vector<double>, boost::parser::detail::nope, boost::parser::detail::nope>, boost::parser::omit_parser<boost::parser::char_parser<char>>, boost::parser::omit_parser<boost::parser::char_parser<char>>>, std::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>>, std::tuple<std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>>>, boost::parser::seq_parser<std::tuple<boost::parser::omit_parser<boost::parser::string_parser<const char *, boost::parser::detail::text::null_sentinel_t>>, boost::parser::omit_parser<boost::parser::char_parser<char>>, boost::parser::rule_parser<false, ester::circuit::library_parser::name, std::basic_string_view<char>, boost::parser::detail::nope, boost::parser::detail::nope>, boost::parser::omit_parser<boost::parser::char_parser<char>>>, std::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>>, std::tuple<std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>>>>, boost::parser::detail::nope>>::operator()<const char *, const char *, boost::parser::detail::parse_context<false, false, const char *, const char *, boost::parser::default_error_handler, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, ester::circuit::library_parser::lu_table_template_1, ester::circuit::library_parser::lu_table_template_1_tag>, boost::parser::detail::null_parser, ester::circuit::library_parser::lu_table_template_1>' requested here  
  237 | BOOST_PARSER_DEFINE_RULES(lu_table_template_1_rule)  
      | ^  
/scratch_raid/tobitom/ester/build/_deps/boost-src/libs/parser/include/boost/parser/parser.hpp:6088:30: note: expanded from macro 'BOOST_PARSER_DEFINE_RULES'  
 6088 |     BOOST_PARSER_PP_FOR_EACH(BOOST_PARSER_DEFINE_IMPL, _, __VA_ARGS__)  
      |                              ^  
/scratch_raid/tobitom/ester/build/_deps/boost-src/libs/parser/include/boost/parser/parser.hpp:5332:29: note: in instantiation of function template specialization 'ester::circuit::library_parser::parse_rule<const char *, const char *, boost::parser::detail::parse_context<false, false, const char *, const char *, boost::parser::default_error_handler, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, ester::circuit::library_parser::lu_table_template_1, ester::circuit::library_parser::lu_table_template_1_tag>, boost::parser::detail::null_parser>' requested here  
 5332 |                 auto attr = parse_rule(  
      |                             ^  
/scratch_raid/tobitom/ester/build/_deps/boost-src/libs/parser/include/boost/parser/parser.hpp:5752:28: note: in instantiation of function template specialization 'boost::parser::rule_parser<false, ester::circuit::library_parser::lu_table_template_1_tag, ester::circuit::library_parser::lu_table_template_1, boost::parser::detail::nope, boost::parser::detail::nope>::call<const char *, const char *, boost::parser::detail::parse_context<false, false, const char *, const char *, boost::parser::default_error_handler>, boost::parser::detail::null_parser>' requested here  
 5752 |             return parser_.call(first, last, context, skip, flags, success);  
      |                            ^  
/scratch_raid/tobitom/ester/build/_deps/boost-src/libs/parser/include/boost/parser/parser.hpp:3622:14: note: candidate template ignored: could not match 'tuple<Ts...>' (aka 'tuple<type-parameter-0-4...>') against 'ester::circuit::library_parser::lu_table_template_1'  
 3622 |         void call_impl(  
      |              ^  
/scratch_raid/tobitom/ester/build/_deps/boost-src/libs/parser/include/boost/parser/parser.hpp:3552:17: error: no matching member function for call to 'call_impl'  
 3552 |                 call_impl(  
      |                 ^~~~~~~~~  
/scratch_raid/tobitom/ester/build/_deps/boost-src/libs/parser/include/boost/parser/parser.hpp:5772:21: note: in instantiation of function template specialization 'boost::parser::perm_parser<std::tuple<boost::parser::seq_parser<std::tuple<boost::parser::omit_parser<boost::parser::string_parser<const char *, boost::parser::detail::text::null_sentinel_t>>, boost::parser::omit_parser<boost::parser::char_parser<char>>, boost::parser::rule_parser<false, ester::circuit::library_parser::qd_vec, std::vector<double>, boost::parser::detail::nope, boost::parser::detail::nope>, boost::parser::omit_parser<boost::parser::char_parser<char>>, boost::parser::omit_parser<boost::parser::char_parser<char>>>, std::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>>, std::tuple<std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>>>, boost::parser::seq_parser<std::tuple<boost::parser::omit_parser<boost::parser::string_parser<const char *, boost::parser::detail::text::null_sentinel_t>>, boost::parser::omit_parser<boost::parser::char_parser<char>>, boost::parser::rule_parser<false, ester::circuit::library_parser::name, std::basic_string_view<char>, boost::parser::detail::nope, boost::parser::detail::nope>, boost::parser::omit_parser<boost::parser::char_parser<char>>>, std::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>>, std::tuple<std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>>>>, boost::parser::detail::nope>::call<const char *, const char *, boost::parser::detail::parse_context<true, false, const char *, const char *, boost::parser::default_error_handler, const boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, ester::circuit::library_parser::lu_table_template_1, ester::circuit::library_parser::lu_table_template_1_tag>, boost::parser::parser_interface<boost::parser::rule_parser<false, ester::circuit::library_parser::skipper, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>>, ester::circuit::library_parser::lu_table_template_1>' requested here  
 5772 |             parser_.call(first, last, context, skip, flags, success, attr);  
      |                     ^  
/scratch_raid/tobitom/ester/src/circuit/include/ester/circuit/library_parser.hpp:237:1: note: in instantiation of function template specialization 'boost::parser::parser_interface<boost::parser::perm_parser<std::tuple<boost::parser::seq_parser<std::tuple<boost::parser::omit_parser<boost::parser::string_parser<const char *, boost::parser::detail::text::null_sentinel_t>>, boost::parser::omit_parser<boost::parser::char_parser<char>>, boost::parser::rule_parser<false, ester::circuit::library_parser::qd_vec, std::vector<double>, boost::parser::detail::nope, boost::parser::detail::nope>, boost::parser::omit_parser<boost::parser::char_parser<char>>, boost::parser::omit_parser<boost::parser::char_parser<char>>>, std::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>>, std::tuple<std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>>>, boost::parser::seq_parser<std::tuple<boost::parser::omit_parser<boost::parser::string_parser<const char *, boost::parser::detail::text::null_sentinel_t>>, boost::parser::omit_parser<boost::parser::char_parser<char>>, boost::parser::rule_parser<false, ester::circuit::library_parser::name, std::basic_string_view<char>, boost::parser::detail::nope, boost::parser::detail::nope>, boost::parser::omit_parser<boost::parser::char_parser<char>>>, std::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>>, std::tuple<std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>>>>, boost::parser::detail::nope>>::operator()<const char *, const char *, boost::parser::detail::parse_context<true, false, const char *, const char *, boost::parser::default_error_handler, const boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, ester::circuit::library_parser::lu_table_template_1, ester::circuit::library_parser::lu_table_template_1_tag>, boost::parser::parser_interface<boost::parser::rule_parser<false, ester::circuit::library_parser::skipper, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>>, ester::circuit::library_parser::lu_table_template_1>' requested here  
  237 | BOOST_PARSER_DEFINE_RULES(lu_table_template_1_rule)  
      | ^  
/scratch_raid/tobitom/ester/build/_deps/boost-src/libs/parser/include/boost/parser/parser.hpp:6088:30: note: expanded from macro 'BOOST_PARSER_DEFINE_RULES'  
 6088 |     BOOST_PARSER_PP_FOR_EACH(BOOST_PARSER_DEFINE_IMPL, _, __VA_ARGS__)  
      |                              ^  
/scratch_raid/tobitom/ester/build/_deps/boost-src/libs/parser/include/boost/parser/parser.hpp:5332:29: note: in instantiation of function template specialization 'ester::circuit::library_parser::parse_rule<const char *, const char *, boost::parser::detail::parse_context<true, false, const char *, const char *, boost::parser::default_error_handler, const boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, ester::circuit::library_parser::lu_table_template_1, ester::circuit::library_parser::lu_table_template_1_tag>, boost::parser::parser_interface<boost::parser::rule_parser<false, ester::circuit::library_parser::skipper, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>>>' requested here  
 5332 |                 auto attr = parse_rule(  
      |                             ^  
/scratch_raid/tobitom/ester/build/_deps/boost-src/libs/parser/include/boost/parser/parser.hpp:5752:28: note: in instantiation of function template specialization 'boost::parser::rule_parser<false, ester::circuit::library_parser::lu_table_template_1_tag, ester::circuit::library_parser::lu_table_template_1, boost::parser::detail::nope, boost::parser::detail::nope>::call<const char *, const char *, boost::parser::detail::parse_context<true, false, const char *, const char *, boost::parser::default_error_handler, const boost::parser::detail::nope>, boost::parser::parser_interface<boost::parser::rule_parser<false, ester::circuit::library_parser::skipper, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>>>' requested here  
 5752 |             return parser_.call(first, last, context, skip, flags, success);  
      |                            ^  
/scratch_raid/tobitom/ester/build/_deps/boost-src/libs/parser/include/boost/parser/parser.hpp:3622:14: note: candidate template ignored: could not match 'tuple<Ts...>' (aka 'tuple<type-parameter-0-4...>') against 'ester::circuit::library_parser::lu_table_template_1'  
 3622 |         void call_impl(  
      |              ^  
/scratch_raid/tobitom/ester/build/_deps/boost-src/libs/parser/include/boost/parser/parser.hpp:3552:17: error: no matching member function for call to 'call_impl'  
 3552 |                 call_impl(  
      |                 ^~~~~~~~~  
/scratch_raid/tobitom/ester/build/_deps/boost-src/libs/parser/include/boost/parser/parser.hpp:5772:21: note: in instantiation of function template specialization 'boost::parser::perm_parser<std::tuple<boost::parser::seq_parser<std::tuple<boost::parser::omit_parser<boost::parser::string_parser<const char *, boost::parser::detail::text::null_sentinel_t>>, boost::parser::omit_parser<boost::parser::char_parser<char>>, boost::parser::rule_parser<false, ester::circuit::library_parser::qd_vec, std::vector<double>, boost::parser::detail::nope, boost::parser::detail::nope>, boost::parser::omit_parser<boost::parser::char_parser<char>>, boost::parser::omit_parser<boost::parser::char_parser<char>>>, std::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>>, std::tuple<std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>>>, boost::parser::seq_parser<std::tuple<boost::parser::omit_parser<boost::parser::string_parser<const char *, boost::parser::detail::text::null_sentinel_t>>, boost::parser::omit_parser<boost::parser::char_parser<char>>, boost::parser::rule_parser<false, ester::circuit::library_parser::name, std::basic_string_view<char>, boost::parser::detail::nope, boost::parser::detail::nope>, boost::parser::omit_parser<boost::parser::char_parser<char>>>, std::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>>, std::tuple<std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>>>>, boost::parser::detail::nope>::call<const char *, const char *, boost::parser::detail::parse_context<false, false, const char *, const char *, boost::parser::default_error_handler, const boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, ester::circuit::library_parser::lu_table_template_1, ester::circuit::library_parser::lu_table_template_1_tag>, boost::parser::parser_interface<boost::parser::rule_parser<false, ester::circuit::library_parser::skipper, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>>, ester::circuit::library_parser::lu_table_template_1>' requested here  
 5772 |             parser_.call(first, last, context, skip, flags, success, attr);  
      |                     ^  
/scratch_raid/tobitom/ester/src/circuit/include/ester/circuit/library_parser.hpp:237:1: note: in instantiation of function template specialization 'boost::parser::parser_interface<boost::parser::perm_parser<std::tuple<boost::parser::seq_parser<std::tuple<boost::parser::omit_parser<boost::parser::string_parser<const char *, boost::parser::detail::text::null_sentinel_t>>, boost::parser::omit_parser<boost::parser::char_parser<char>>, boost::parser::rule_parser<false, ester::circuit::library_parser::qd_vec, std::vector<double>, boost::parser::detail::nope, boost::parser::detail::nope>, boost::parser::omit_parser<boost::parser::char_parser<char>>, boost::parser::omit_parser<boost::parser::char_parser<char>>>, std::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>>, std::tuple<std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>>>, boost::parser::seq_parser<std::tuple<boost::parser::omit_parser<boost::parser::string_parser<const char *, boost::parser::detail::text::null_sentinel_t>>, boost::parser::omit_parser<boost::parser::char_parser<char>>, boost::parser::rule_parser<false, ester::circuit::library_parser::name, std::basic_string_view<char>, boost::parser::detail::nope, boost::parser::detail::nope>, boost::parser::omit_parser<boost::parser::char_parser<char>>>, std::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>>, std::tuple<std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>>>>, boost::parser::detail::nope>>::operator()<const char *, const char *, boost::parser::detail::parse_context<false, false, const char *, const char *, boost::parser::default_error_handler, const boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, ester::circuit::library_parser::lu_table_template_1, ester::circuit::library_parser::lu_table_template_1_tag>, boost::parser::parser_interface<boost::parser::rule_parser<false, ester::circuit::library_parser::skipper, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>>, ester::circuit::library_parser::lu_table_template_1>' requested here  
  237 | BOOST_PARSER_DEFINE_RULES(lu_table_template_1_rule)  
      | ^  
/scratch_raid/tobitom/ester/build/_deps/boost-src/libs/parser/include/boost/parser/parser.hpp:6088:30: note: expanded from macro 'BOOST_PARSER_DEFINE_RULES'  
 6088 |     BOOST_PARSER_PP_FOR_EACH(BOOST_PARSER_DEFINE_IMPL, _, __VA_ARGS__)  
      |                              ^  
/scratch_raid/tobitom/ester/build/_deps/boost-src/libs/parser/include/boost/parser/parser.hpp:5332:29: note: in instantiation of function template specialization 'ester::circuit::library_parser::parse_rule<const char *, const char *, boost::parser::detail::parse_context<false, false, const char *, const char *, boost::parser::default_error_handler, const boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, ester::circuit::library_parser::lu_table_template_1, ester::circuit::library_parser::lu_table_template_1_tag>, boost::parser::parser_interface<boost::parser::rule_parser<false, ester::circuit::library_parser::skipper, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>>>' requested here  
 5332 |                 auto attr = parse_rule(  
      |                             ^  
/scratch_raid/tobitom/ester/build/_deps/boost-src/libs/parser/include/boost/parser/parser.hpp:5752:28: note: in instantiation of function template specialization 'boost::parser::rule_parser<false, ester::circuit::library_parser::lu_table_template_1_tag, ester::circuit::library_parser::lu_table_template_1, boost::parser::detail::nope, boost::parser::detail::nope>::call<const char *, const char *, boost::parser::detail::parse_context<false, false, const char *, const char *, boost::parser::default_error_handler, const boost::parser::detail::nope>, boost::parser::parser_interface<boost::parser::rule_parser<false, ester::circuit::library_parser::skipper, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>>>' requested here  
 5752 |             return parser_.call(first, last, context, skip, flags, success);  
      |                            ^  
/scratch_raid/tobitom/ester/build/_deps/boost-src/libs/parser/include/boost/parser/parser.hpp:3622:14: note: candidate template ignored: could not match 'tuple<Ts...>' (aka 'tuple<type-parameter-0-4...>') against 'ester::circuit::library_parser::lu_table_template_1'  
 3622 |         void call_impl(  
      |              ^  
```

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-10-13 01:30:04 UTC  
> Url: https://github.com/boostorg/parser/issues/268#issuecomment-3395599261  

There are collapsing/combining rules for op>> that make no sense for op||.  See the documentation on those op>> rules.  So you cannot expect them to be always the same.  The case above doesn't seem to be one of those, however.  Could you post the rest of the code, exercising these parsers?

---

## Comment 2

> Username: andreasbuhr  
> Created at: 2025-10-14 13:56:51 UTC  
> Url: https://github.com/boostorg/parser/issues/268#issuecomment-3402025103  

My analysis is that this is not about the collapsing rules. I think this is a bug in perm_parser.  
  
In case the "Attribute" type to be generated (in this case "lu_table_template_1") can be constructed from "result_t" (in this case std::tuple<std::vector<double>,std::string_view>), the result-object of type "Attribute" is given to perm_parser::call_impl() in  
https://github.com/boostorg/parser/blob/d873d7ea8076d95b816cd9557553672ca9689e9b/include/boost/parser/parser.hpp#L3693  
  
However, perm_parser::call_impl() requires the result-object to be an std::tuple. Which it isn't.  
See https://github.com/boostorg/parser/blob/d873d7ea8076d95b816cd9557553672ca9689e9b/include/boost/parser/parser.hpp#L3763  
  
I'll created a pull request demonstrating the problem in https://github.com/boostorg/parser/pull/282.

---

## Comment 3

> Username: tzlaine  
> Created at: 2025-10-31 17:40:29 UTC  
> Url: https://github.com/boostorg/parser/issues/268#issuecomment-3474150933  

You're quite right!  Thanks, Andreas.
