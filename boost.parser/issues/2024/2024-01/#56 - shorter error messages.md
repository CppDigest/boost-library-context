# #56 - shorter error messages? [Open]

> Username: akrzemi1  
> Created at: 2024-01-08 18:32:52 UTC  
> Updated at: 2024-02-21 22:09:08 UTC  
> Url: https://github.com/boostorg/parser/issues/56  

When I compile the example from the other issue:  
  
```c++  
#ifdef WITH_BOOST_SPIRIT  
#include <boost/fusion/adapted/struct/adapt_struct.hpp>  
#include <boost/spirit/include/qi.hpp>  
namespace bp = boost::spirit::qi;  
#else  
#include <boost/parser/parser.hpp>  
namespace bp = boost::parser;  
#endif  
  
int main()  
{  
#ifdef WITH_BOOST_SPIRIT  
  auto alnum = bp::ascii::alnum;  
  auto alpha = bp::alpha;  
#else  
  auto alnum = bp::ascii::alnum;  
  auto alpha = bp::ascii::alpha;  
#endif  
  
  auto myParser  = (alpha >> alpha >> -alnum);  
  
  std::string input = "MM2";  
  std::string result;  
  
#ifdef WITH_BOOST_SPIRIT  
  auto begin = input.begin();  
  bool b = bp::phrase_parse(begin, input.end(), myParser, bp::ascii::space, result);  
#else  
  auto b = bp::parse(input, myParser, bp::ws, result);  
#endif  
  
  if (b)  
    std::cout << ":-) " << result << std::endl;  
  else  
    std::cout << ":-(" << std::endl;  
}  
```  
  
I get the following error message. It is very long, and I am convinced that it could be made shorter with some metapprogramming tricks.  
  
  
```  
In file included from ../boost/boost/hana/functional/partial.hpp:13,  
                 from ../boost/boost/hana/fold_left.hpp:19,  
                 from ../boost/boost/hana/concept/foldable.hpp:19,  
                 from ../boost/boost/hana/core/to.hpp:16,  
                 from ../boost/boost/hana/bool.hpp:17,  
                 from ../boost/boost/hana/string.hpp:15,  
                 from ../boost/boost/hana/detail/struct_macros.hpp:28,  
                 from ../boost/boost/hana/adapt_adt.hpp:15,  
                 from ../boost/boost/hana.hpp:59,  
                 from ../parser/include/boost/parser/tuple.hpp:27,  
                 from ../parser/include/boost/parser/detail/printing.hpp:5,  
                 from ../parser/include/boost/parser/error_handling.hpp:5,  
                 from ../parser/include/boost/parser/parser.hpp:6,  
                 from ./main.cpp:6:  
../boost/boost/hana/basic_tuple.hpp: In instantiation of ‘constexpr decltype(auto) boost::hana::at_c(basic_tuple<Xs ...>&) [with long unsigned int n = 1; Xs = {std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >}]’:  
../boost/boost/hana/tuple.hpp:287:29:   required from ‘constexpr decltype(auto) boost::hana::at_c(tuple<Xs ...>&) [with long unsigned int n = 1; Xs = {std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >}]’  
../parser/include/boost/parser/tuple.hpp:161:33:   required from ‘constexpr decltype(auto) boost::parser::detail::tuple_get(boost::hana::tuple<Args ...>&, boost::parser::integral_constant<T, I>) [with T = long long int; T I = 1; Args = {std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >}; boost::parser::integral_constant<T, I> = boost::hana::integral_constant<long long int, 1>]’  
../parser/include/boost/parser/tuple.hpp:331:37:   required from ‘constexpr decltype(auto) boost::parser::get(T&&, integral_constant<U, I>) [with T = boost::hana::tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >&; U = long long int; U I = 1; integral_constant<U, I> = boost::hana::integral_constant<long long int, 1>]’  
../parser/include/boost/parser/parser.hpp:3489:41:   required from ‘boost::parser::seq_parser<boost::hana::tuple<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::parser::opt_parser<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alnum>, void> > >, boost::hana::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true> > >::call_impl<false, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, boost::parser::detail::parse_context<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, boost::parser::default_error_handler, const boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, void, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>, boost::parser::parser_interface<boost::parser::ws_parser<false> >, boost::hana::tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, boost::hana::tuple<boost::hana::integral_constant<long long int, 0>, boost::hana::integral_constant<long long int, 1>, boost::hana::integral_constant<long long int, 2> >, boost::hana::tuple<std::integral_constant<bool, false>, std::integral_constant<bool, false>, std::integral_constant<bool, false> > >(std::bool_constant<false>, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >&, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, const boost::parser::detail::parse_context<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, boost::parser::default_error_handler, const boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, void, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>&, const boost::parser::parser_interface<boost::parser::ws_parser<false> >&, boost::parser::detail::flags, bool&, boost::hana::tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >&, const boost::hana::tuple<boost::hana::integral_constant<long long int, 0>, boost::hana::integral_constant<long long int, 1>, boost::hana::integral_constant<long long int, 2> >&, const boost::hana::tuple<std::integral_constant<bool, false>, std::integral_constant<bool, false>, std::integral_constant<bool, false> >&) const::<lambda(const auto:38&)> [with auto:38 = boost::hana::tuple<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::hana::integral_constant<long long int, 1>, std::integral_constant<bool, false>, std::integral_constant<bool, true> >]’  
../parser/include/boost/parser/detail/hl.hpp:67:25:   required from ‘constexpr void boost::parser::detail::hl::for_each_impl(const Tuple&, F&&, std::integer_sequence<long unsigned int, _Idx ...>) [with F = boost::parser::seq_parser<boost::hana::tuple<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::parser::opt_parser<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alnum>, void> > >, boost::hana::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true> > >::call_impl<false, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, boost::parser::detail::parse_context<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, boost::parser::default_error_handler, const boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, void, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>, boost::parser::parser_interface<boost::parser::ws_parser<false> >, boost::hana::tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, boost::hana::tuple<boost::hana::integral_constant<long long int, 0>, boost::hana::integral_constant<long long int, 1>, boost::hana::integral_constant<long long int, 2> >, boost::hana::tuple<std::integral_constant<bool, false>, std::integral_constant<bool, false>, std::integral_constant<bool, false> > >(std::bool_constant<false>, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >&, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, const boost::parser::detail::parse_context<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, boost::parser::default_error_handler, const boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, void, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>&, const boost::parser::parser_interface<boost::parser::ws_parser<false> >&, boost::parser::detail::flags, bool&, boost::hana::tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >&, const boost::hana::tuple<boost::hana::integral_constant<long long int, 0>, boost::hana::integral_constant<long long int, 1>, boost::hana::integral_constant<long long int, 2> >&, const boost::hana::tuple<std::integral_constant<bool, false>, std::integral_constant<bool, false>, std::integral_constant<bool, false> >&) const::<lambda(const auto:38&)>&; Tuple = boost::hana::tuple<boost::hana::tuple<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::hana::integral_constant<long long int, 0>, std::integral_constant<bool, false>, std::integral_constant<bool, true> >, boost::hana::tuple<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::hana::integral_constant<long long int, 1>, std::integral_constant<bool, false>, std::integral_constant<bool, true> >, boost::hana::tuple<boost::parser::opt_parser<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alnum>, void> >, boost::hana::integral_constant<long long int, 2>, std::integral_constant<bool, false>, std::integral_constant<bool, true> > >; long unsigned int ...I = {0, 1, 2}]’  
../parser/include/boost/parser/detail/hl.hpp:93:26:   [ skipping 2 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
../parser/include/boost/parser/parser.hpp:3403:26:   required from ‘void boost::parser::seq_parser<ParserTuple, BacktrackingTuple>::call(std::bool_constant<UseCallbacks>, Iter&, Sentinel, const Context&, const SkipParser&, boost::parser::detail::flags, bool&, Attribute&) const [with bool UseCallbacks = false; Iter = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Sentinel = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::parser::detail::parse_context<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, boost::parser::default_error_handler, const boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, void, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>; SkipParser = boost::parser::parser_interface<boost::parser::ws_parser<false> >; Attribute = std::__cxx11::basic_string<char>; ParserTuple = boost::hana::tuple<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::parser::opt_parser<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alnum>, void> > >; BacktrackingTuple = boost::hana::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true> >; std::bool_constant<UseCallbacks> = std::integral_constant<bool, false>]’  
../parser/include/boost/parser/parser.hpp:4681:25:   required from ‘void boost::parser::parser_interface<Parser, GlobalState, ErrorHandler>::operator()(std::bool_constant<UseCallbacks>, Iter&, Sentinel, const Context&, const SkipParserType&, boost::parser::detail::flags, bool&, Attribute&) const [with bool UseCallbacks = false; Iter = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Sentinel = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::parser::detail::parse_context<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, boost::parser::default_error_handler, const boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, void, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>; SkipParserType = boost::parser::parser_interface<boost::parser::ws_parser<false> >; Attribute = std::__cxx11::basic_string<char>; Parser = boost::parser::seq_parser<boost::hana::tuple<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::parser::opt_parser<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alnum>, void> > >, boost::hana::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true> > >; GlobalState = boost::parser::detail::nope; ErrorHandler = boost::parser::default_error_handler; std::bool_constant<UseCallbacks> = std::integral_constant<bool, false>]’  
../parser/include/boost/parser/parser.hpp:2142:23:   required from ‘bool boost::parser::detail::skip_parse_impl(Iter&, Sentinel, const Parser&, const SkipParser&, const ErrorHandler&, Attr&) [with bool Debug = true; Iter = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Sentinel = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Parser = boost::parser::parser_interface<boost::parser::seq_parser<boost::hana::tuple<boost::parser::char_parser<ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::parser::char_parser<ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::parser::opt_parser<boost::parser::char_parser<ascii_char_class<boost::parser::detail::ascii_char_class_t::alnum>, void> > >, boost::hana::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true> > >, nope, boost::parser::default_error_handler>; SkipParser = boost::parser::parser_interface<boost::parser::ws_parser<false> >; Attr = std::__cxx11::basic_string<char>; ErrorHandler = boost::parser::default_error_handler]’  
../parser/include/boost/parser/parser.hpp:6912:53:   required from ‘bool boost::parser::prefix_parse(I&, S, const parser_interface<Parser, GlobalState, ErrorHandler>&, const parser_interface<SkipParser>&, Attr&, trace) [with I = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; S = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Parser = seq_parser<boost::hana::tuple<char_parser<detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, char_parser<detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, opt_parser<char_parser<detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alnum>, void> > >, boost::hana::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true> > >; GlobalState = detail::nope; ErrorHandler = default_error_handler; SkipParser = ws_parser<false>; Attr = std::__cxx11::basic_string<char>; Enable = void]’  
../parser/include/boost/parser/parser.hpp:6985:33:   required from ‘bool boost::parser::parse(const R&, const parser_interface<Parser, GlobalState, ErrorHandler>&, const parser_interface<SkipParser>&, Attr&, trace) [with R = std::__cxx11::basic_string<char>; Parser = seq_parser<boost::hana::tuple<char_parser<detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, char_parser<detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, opt_parser<char_parser<detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alnum>, void> > >, boost::hana::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true> > >; GlobalState = detail::nope; ErrorHandler = default_error_handler; SkipParser = ws_parser<false>; Attr = std::__cxx11::basic_string<char>; Enable = void]’  
./main.cpp:29:21:   required from here  
../boost/boost/hana/basic_tuple.hpp:225:47: error: no matching function for call to ‘ebo_get<boost::hana::detail::bti<1> >(boost::hana::basic_tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >&)’  
  225 |         return detail::ebo_get<detail::bti<n>>(xs);  
      |                ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~  
In file included from ../boost/boost/hana/pair.hpp:17,  
                 from ../boost/boost/hana/detail/struct_macros.hpp:27:  
../boost/boost/hana/detail/ebo.hpp:104:19: note: candidate: ‘template<class K, class V> constexpr V&& _hana::ebo_get(ebo<K, V, false>&&)’  
  104 |     constexpr V&& ebo_get(ebo<K, V, false>&& x)  
      |                   ^~~~~~~  
../boost/boost/hana/detail/ebo.hpp:104:19: note:   template argument deduction/substitution failed:  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   template argument ‘0’ does not match ‘1’  
  225 |         return detail::ebo_get<detail::bti<n>>(xs);  
      |                ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   ‘boost::hana::basic_tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >’ is not derived from ‘_hana::ebo<boost::hana::detail::bti<1>, V, false>’  
../boost/boost/hana/detail/ebo.hpp:100:18: note: candidate: ‘template<class K, class V> constexpr V& _hana::ebo_get(ebo<K, V, false>&)’  
  100 |     constexpr V& ebo_get(ebo<K, V, false>& x)  
      |                  ^~~~~~~  
../boost/boost/hana/detail/ebo.hpp:100:18: note:   template argument deduction/substitution failed:  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   template argument ‘0’ does not match ‘1’  
  225 |         return detail::ebo_get<detail::bti<n>>(xs);  
      |                ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   ‘boost::hana::basic_tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >’ is not derived from ‘_hana::ebo<boost::hana::detail::bti<1>, V, false>’  
../boost/boost/hana/detail/ebo.hpp:96:24: note: candidate: ‘template<class K, class V> constexpr const V& _hana::ebo_get(const ebo<K, V, false>&)’  
   96 |     constexpr V const& ebo_get(ebo<K, V, false> const& x)  
      |                        ^~~~~~~  
../boost/boost/hana/detail/ebo.hpp:96:24: note:   template argument deduction/substitution failed:  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   template argument ‘0’ does not match ‘1’  
  225 |         return detail::ebo_get<detail::bti<n>>(xs);  
      |                ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   ‘boost::hana::basic_tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >’ is not derived from ‘const _hana::ebo<boost::hana::detail::bti<1>, V, false>’  
../boost/boost/hana/detail/ebo.hpp:91:19: note: candidate: ‘template<class K, class V> constexpr V&& _hana::ebo_get(ebo<K, V, true>&&)’  
   91 |     constexpr V&& ebo_get(ebo<K, V, true>&& x)  
      |                   ^~~~~~~  
../boost/boost/hana/detail/ebo.hpp:91:19: note:   template argument deduction/substitution failed:  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   template argument ‘0’ does not match ‘1’  
  225 |         return detail::ebo_get<detail::bti<n>>(xs);  
      |                ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   ‘boost::hana::basic_tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >’ is not derived from ‘_hana::ebo<boost::hana::detail::bti<1>, V, true>’  
../boost/boost/hana/detail/ebo.hpp:87:18: note: candidate: ‘template<class K, class V> constexpr V& _hana::ebo_get(ebo<K, V, true>&)’  
   87 |     constexpr V& ebo_get(ebo<K, V, true>& x)  
      |                  ^~~~~~~  
../boost/boost/hana/detail/ebo.hpp:87:18: note:   template argument deduction/substitution failed:  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   template argument ‘0’ does not match ‘1’  
  225 |         return detail::ebo_get<detail::bti<n>>(xs);  
      |                ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   ‘boost::hana::basic_tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >’ is not derived from ‘_hana::ebo<boost::hana::detail::bti<1>, V, true>’  
../boost/boost/hana/detail/ebo.hpp:83:24: note: candidate: ‘template<class K, class V> constexpr const V& _hana::ebo_get(const ebo<K, V, true>&)’  
   83 |     constexpr V const& ebo_get(ebo<K, V, true> const& x)  
      |                        ^~~~~~~  
../boost/boost/hana/detail/ebo.hpp:83:24: note:   template argument deduction/substitution failed:  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   template argument ‘0’ does not match ‘1’  
  225 |         return detail::ebo_get<detail::bti<n>>(xs);  
      |                ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   ‘boost::hana::basic_tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >’ is not derived from ‘const _hana::ebo<boost::hana::detail::bti<1>, V, true>’  
../boost/boost/hana/basic_tuple.hpp: In instantiation of ‘constexpr decltype(auto) boost::hana::at_c(basic_tuple<Xs ...>&) [with long unsigned int n = 2; Xs = {std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >}]’:  
../boost/boost/hana/tuple.hpp:287:29:   required from ‘constexpr decltype(auto) boost::hana::at_c(tuple<Xs ...>&) [with long unsigned int n = 2; Xs = {std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >}]’  
../parser/include/boost/parser/tuple.hpp:161:33:   required from ‘constexpr decltype(auto) boost::parser::detail::tuple_get(boost::hana::tuple<Args ...>&, boost::parser::integral_constant<T, I>) [with T = long long int; T I = 2; Args = {std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >}; boost::parser::integral_constant<T, I> = boost::hana::integral_constant<long long int, 2>]’  
../parser/include/boost/parser/tuple.hpp:331:37:   required from ‘constexpr decltype(auto) boost::parser::get(T&&, integral_constant<U, I>) [with T = boost::hana::tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >&; U = long long int; U I = 2; integral_constant<U, I> = boost::hana::integral_constant<long long int, 2>]’  
../parser/include/boost/parser/parser.hpp:3489:41:   required from ‘boost::parser::seq_parser<boost::hana::tuple<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::parser::opt_parser<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alnum>, void> > >, boost::hana::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true> > >::call_impl<false, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, boost::parser::detail::parse_context<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, boost::parser::default_error_handler, const boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, void, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>, boost::parser::parser_interface<boost::parser::ws_parser<false> >, boost::hana::tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, boost::hana::tuple<boost::hana::integral_constant<long long int, 0>, boost::hana::integral_constant<long long int, 1>, boost::hana::integral_constant<long long int, 2> >, boost::hana::tuple<std::integral_constant<bool, false>, std::integral_constant<bool, false>, std::integral_constant<bool, false> > >(std::bool_constant<false>, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >&, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, const boost::parser::detail::parse_context<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, boost::parser::default_error_handler, const boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, void, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>&, const boost::parser::parser_interface<boost::parser::ws_parser<false> >&, boost::parser::detail::flags, bool&, boost::hana::tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >&, const boost::hana::tuple<boost::hana::integral_constant<long long int, 0>, boost::hana::integral_constant<long long int, 1>, boost::hana::integral_constant<long long int, 2> >&, const boost::hana::tuple<std::integral_constant<bool, false>, std::integral_constant<bool, false>, std::integral_constant<bool, false> >&) const::<lambda(const auto:38&)> [with auto:38 = boost::hana::tuple<boost::parser::opt_parser<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alnum>, void> >, boost::hana::integral_constant<long long int, 2>, std::integral_constant<bool, false>, std::integral_constant<bool, true> >]’  
../parser/include/boost/parser/detail/hl.hpp:67:25:   required from ‘constexpr void boost::parser::detail::hl::for_each_impl(const Tuple&, F&&, std::integer_sequence<long unsigned int, _Idx ...>) [with F = boost::parser::seq_parser<boost::hana::tuple<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::parser::opt_parser<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alnum>, void> > >, boost::hana::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true> > >::call_impl<false, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, boost::parser::detail::parse_context<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, boost::parser::default_error_handler, const boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, void, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>, boost::parser::parser_interface<boost::parser::ws_parser<false> >, boost::hana::tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, boost::hana::tuple<boost::hana::integral_constant<long long int, 0>, boost::hana::integral_constant<long long int, 1>, boost::hana::integral_constant<long long int, 2> >, boost::hana::tuple<std::integral_constant<bool, false>, std::integral_constant<bool, false>, std::integral_constant<bool, false> > >(std::bool_constant<false>, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >&, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, const boost::parser::detail::parse_context<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, boost::parser::default_error_handler, const boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, void, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>&, const boost::parser::parser_interface<boost::parser::ws_parser<false> >&, boost::parser::detail::flags, bool&, boost::hana::tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >&, const boost::hana::tuple<boost::hana::integral_constant<long long int, 0>, boost::hana::integral_constant<long long int, 1>, boost::hana::integral_constant<long long int, 2> >&, const boost::hana::tuple<std::integral_constant<bool, false>, std::integral_constant<bool, false>, std::integral_constant<bool, false> >&) const::<lambda(const auto:38&)>&; Tuple = boost::hana::tuple<boost::hana::tuple<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::hana::integral_constant<long long int, 0>, std::integral_constant<bool, false>, std::integral_constant<bool, true> >, boost::hana::tuple<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::hana::integral_constant<long long int, 1>, std::integral_constant<bool, false>, std::integral_constant<bool, true> >, boost::hana::tuple<boost::parser::opt_parser<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alnum>, void> >, boost::hana::integral_constant<long long int, 2>, std::integral_constant<bool, false>, std::integral_constant<bool, true> > >; long unsigned int ...I = {0, 1, 2}]’  
../parser/include/boost/parser/detail/hl.hpp:93:26:   [ skipping 2 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
../parser/include/boost/parser/parser.hpp:3403:26:   required from ‘void boost::parser::seq_parser<ParserTuple, BacktrackingTuple>::call(std::bool_constant<UseCallbacks>, Iter&, Sentinel, const Context&, const SkipParser&, boost::parser::detail::flags, bool&, Attribute&) const [with bool UseCallbacks = false; Iter = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Sentinel = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::parser::detail::parse_context<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, boost::parser::default_error_handler, const boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, void, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>; SkipParser = boost::parser::parser_interface<boost::parser::ws_parser<false> >; Attribute = std::__cxx11::basic_string<char>; ParserTuple = boost::hana::tuple<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::parser::opt_parser<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alnum>, void> > >; BacktrackingTuple = boost::hana::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true> >; std::bool_constant<UseCallbacks> = std::integral_constant<bool, false>]’  
../parser/include/boost/parser/parser.hpp:4681:25:   required from ‘void boost::parser::parser_interface<Parser, GlobalState, ErrorHandler>::operator()(std::bool_constant<UseCallbacks>, Iter&, Sentinel, const Context&, const SkipParserType&, boost::parser::detail::flags, bool&, Attribute&) const [with bool UseCallbacks = false; Iter = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Sentinel = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::parser::detail::parse_context<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >, boost::parser::default_error_handler, const boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, void, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>; SkipParserType = boost::parser::parser_interface<boost::parser::ws_parser<false> >; Attribute = std::__cxx11::basic_string<char>; Parser = boost::parser::seq_parser<boost::hana::tuple<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::parser::opt_parser<boost::parser::char_parser<boost::parser::detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alnum>, void> > >, boost::hana::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true> > >; GlobalState = boost::parser::detail::nope; ErrorHandler = boost::parser::default_error_handler; std::bool_constant<UseCallbacks> = std::integral_constant<bool, false>]’  
../parser/include/boost/parser/parser.hpp:2142:23:   required from ‘bool boost::parser::detail::skip_parse_impl(Iter&, Sentinel, const Parser&, const SkipParser&, const ErrorHandler&, Attr&) [with bool Debug = true; Iter = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Sentinel = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Parser = boost::parser::parser_interface<boost::parser::seq_parser<boost::hana::tuple<boost::parser::char_parser<ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::parser::char_parser<ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, boost::parser::opt_parser<boost::parser::char_parser<ascii_char_class<boost::parser::detail::ascii_char_class_t::alnum>, void> > >, boost::hana::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true> > >, nope, boost::parser::default_error_handler>; SkipParser = boost::parser::parser_interface<boost::parser::ws_parser<false> >; Attr = std::__cxx11::basic_string<char>; ErrorHandler = boost::parser::default_error_handler]’  
../parser/include/boost/parser/parser.hpp:6912:53:   required from ‘bool boost::parser::prefix_parse(I&, S, const parser_interface<Parser, GlobalState, ErrorHandler>&, const parser_interface<SkipParser>&, Attr&, trace) [with I = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; S = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Parser = seq_parser<boost::hana::tuple<char_parser<detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, char_parser<detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, opt_parser<char_parser<detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alnum>, void> > >, boost::hana::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true> > >; GlobalState = detail::nope; ErrorHandler = default_error_handler; SkipParser = ws_parser<false>; Attr = std::__cxx11::basic_string<char>; Enable = void]’  
../parser/include/boost/parser/parser.hpp:6985:33:   required from ‘bool boost::parser::parse(const R&, const parser_interface<Parser, GlobalState, ErrorHandler>&, const parser_interface<SkipParser>&, Attr&, trace) [with R = std::__cxx11::basic_string<char>; Parser = seq_parser<boost::hana::tuple<char_parser<detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, char_parser<detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alpha>, void>, opt_parser<char_parser<detail::ascii_char_class<boost::parser::detail::ascii_char_class_t::alnum>, void> > >, boost::hana::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true> > >; GlobalState = detail::nope; ErrorHandler = default_error_handler; SkipParser = ws_parser<false>; Attr = std::__cxx11::basic_string<char>; Enable = void]’  
./main.cpp:29:21:   required from here  
../boost/boost/hana/basic_tuple.hpp:225:47: error: no matching function for call to ‘ebo_get<boost::hana::detail::bti<2> >(boost::hana::basic_tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >&)’  
../boost/boost/hana/detail/ebo.hpp:104:19: note: candidate: ‘template<class K, class V> constexpr V&& _hana::ebo_get(ebo<K, V, false>&&)’  
  104 |     constexpr V&& ebo_get(ebo<K, V, false>&& x)  
      |                   ^~~~~~~  
../boost/boost/hana/detail/ebo.hpp:104:19: note:   template argument deduction/substitution failed:  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   template argument ‘0’ does not match ‘2’  
  225 |         return detail::ebo_get<detail::bti<n>>(xs);  
      |                ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   ‘boost::hana::basic_tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >’ is not derived from ‘_hana::ebo<boost::hana::detail::bti<2>, V, false>’  
../boost/boost/hana/detail/ebo.hpp:100:18: note: candidate: ‘template<class K, class V> constexpr V& _hana::ebo_get(ebo<K, V, false>&)’  
  100 |     constexpr V& ebo_get(ebo<K, V, false>& x)  
      |                  ^~~~~~~  
../boost/boost/hana/detail/ebo.hpp:100:18: note:   template argument deduction/substitution failed:  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   template argument ‘0’ does not match ‘2’  
  225 |         return detail::ebo_get<detail::bti<n>>(xs);  
      |                ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   ‘boost::hana::basic_tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >’ is not derived from ‘_hana::ebo<boost::hana::detail::bti<2>, V, false>’  
../boost/boost/hana/detail/ebo.hpp:96:24: note: candidate: ‘template<class K, class V> constexpr const V& _hana::ebo_get(const ebo<K, V, false>&)’  
   96 |     constexpr V const& ebo_get(ebo<K, V, false> const& x)  
      |                        ^~~~~~~  
../boost/boost/hana/detail/ebo.hpp:96:24: note:   template argument deduction/substitution failed:  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   template argument ‘0’ does not match ‘2’  
  225 |         return detail::ebo_get<detail::bti<n>>(xs);  
      |                ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   ‘boost::hana::basic_tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >’ is not derived from ‘const _hana::ebo<boost::hana::detail::bti<2>, V, false>’  
../boost/boost/hana/detail/ebo.hpp:91:19: note: candidate: ‘template<class K, class V> constexpr V&& _hana::ebo_get(ebo<K, V, true>&&)’  
   91 |     constexpr V&& ebo_get(ebo<K, V, true>&& x)  
      |                   ^~~~~~~  
../boost/boost/hana/detail/ebo.hpp:91:19: note:   template argument deduction/substitution failed:  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   template argument ‘0’ does not match ‘2’  
  225 |         return detail::ebo_get<detail::bti<n>>(xs);  
      |                ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   ‘boost::hana::basic_tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >’ is not derived from ‘_hana::ebo<boost::hana::detail::bti<2>, V, true>’  
../boost/boost/hana/detail/ebo.hpp:87:18: note: candidate: ‘template<class K, class V> constexpr V& _hana::ebo_get(ebo<K, V, true>&)’  
   87 |     constexpr V& ebo_get(ebo<K, V, true>& x)  
      |                  ^~~~~~~  
../boost/boost/hana/detail/ebo.hpp:87:18: note:   template argument deduction/substitution failed:  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   template argument ‘0’ does not match ‘2’  
  225 |         return detail::ebo_get<detail::bti<n>>(xs);  
      |                ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   ‘boost::hana::basic_tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >’ is not derived from ‘_hana::ebo<boost::hana::detail::bti<2>, V, true>’  
../boost/boost/hana/detail/ebo.hpp:83:24: note: candidate: ‘template<class K, class V> constexpr const V& _hana::ebo_get(const ebo<K, V, true>&)’  
   83 |     constexpr V const& ebo_get(ebo<K, V, true> const& x)  
      |                        ^~~~~~~  
../boost/boost/hana/detail/ebo.hpp:83:24: note:   template argument deduction/substitution failed:  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   template argument ‘0’ does not match ‘2’  
  225 |         return detail::ebo_get<detail::bti<n>>(xs);  
      |                ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~  
../boost/boost/hana/basic_tuple.hpp:225:47: note:   ‘boost::hana::basic_tuple<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >’ is not derived from ‘const _hana::ebo<boost::hana::detail::bti<2>, V, true>’  
```

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-01-08 23:56:51 UTC  
> Url: https://github.com/boostorg/parser/issues/56#issuecomment-1882004432  

Hm.  How exactly?  I'm interested, but I don't see how.  The usual techniques -- SFINAE/concept constraints and static_asserts won't really work very well here, because of the loose matching of attribute types.  If I pass in a `U` instead of a `T` for the attribute, that should work under many circumstances.  However, it's pretty hard to write the constraint/static_assert for a failure like the one above.

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-01-09 00:34:44 UTC  
> Url: https://github.com/boostorg/parser/issues/56#issuecomment-1882061061  

Well, I *did* actually find a place to put a static_assert that gives a likely-useful hint at the kind of error above.  
  
So, Let's keep this issue open; please post more examples as they come up.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2024-01-09 17:48:01 UTC  
> Updated at: 2024-01-09 17:48:33 UTC  
> Url: https://github.com/boostorg/parser/issues/56#issuecomment-1883515749  

From my experience, `static_assert` alone is not enough, as it doesn't stop the compilation. A `static_assert` followed by an enable-if trick, or a tricky template specializaiton does the job.

---

## Comment 4

> Username: tzlaine  
> Created at: 2024-01-10 05:24:39 UTC  
> Url: https://github.com/boostorg/parser/issues/56#issuecomment-1884211682  

I don't know of any snippet of code guaranteed to stop the compile on any implementation, and I tried putting a constraint on the use of the bad index after the static assert, and it did not stop the build, or produce any fewer diagnostics.  I'm open to suggestions though.

---

## Comment 5

> Username: akrzemi1  
> Created at: 2024-01-10 17:12:50 UTC  
> Url: https://github.com/boostorg/parser/issues/56#issuecomment-1885263250  

https://github.com/tzlaine/parser/commit/257dcd36aa5921fe5c74a217aa35b44d627a1721#r136667439

---

## Comment 6

> Username: akrzemi1  
> Created at: 2024-01-12 20:14:15 UTC  
> Url: https://github.com/boostorg/parser/issues/56#issuecomment-1889894348  

Also, maybe after the new assert, use some redundant template instantiation that would display (as error message) the type of the default attribute and the type of the struct that I wanted to parse into.  
  
In a nested parser it is far from obvious what structure is being parsed into.

---

## Comment 7

> Username: akrzemi1  
> Created at: 2024-02-21 22:09:08 UTC  
> Url: https://github.com/boostorg/parser/issues/56#issuecomment-1958093092  

another example with huge error messages in GCC:  
  
```c++  
#include <boost/parser/parser.hpp>  
namespace bp = boost::parser;  
  
int main()  
{  
  auto myParser  = (bp::lower >> bp::lower >> -bp::digi);  
  
  std::string input = "MM2";  
  int result;  
  
  auto b = bp::parse(input, myParser, bp::ws, result);  
  
  if (b)  
    std::cout << ":-) " << result << std::endl;  
  else  
    std::cout << ":-(" << std::endl;  
}  
```
