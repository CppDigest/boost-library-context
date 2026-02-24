# #782 - MSVC (VS 2023) fails to compile example/x3/annotation.cpp with /std:c++20 or /std:c++latest option [Closed]

> Username: voivoid  
> Created at: 2024-01-18 21:27:57 UTC  
> Updated at: 2025-05-10 00:20:51 UTC  
> Closed at: 2024-02-23 23:10:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/782  

MSVC fails to compile [annotation example ](https://www.boost.org/doc/libs/1_83_0/libs/spirit/example/x3/annotation.cpp) if  /std:c++20 or /std:c++latest is set. Everything is fine with /std:c++17.  
  
See [compiler explorer](https://godbolt.org/z/hGs9eEr54) to get the bug reproduction  
  
Unfortunately the error message ( can be seen in the provided link ) is pretty cryptic so I'm not sure whether it's a MSVC or boost::spirit problem.

---

## Comment 1

> Username: StephanTLavavej  
> Created at: 2024-02-21 20:10:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/782#issuecomment-1957821629  

This was also reported to MSVC as [DevCom-10565526](https://developercommunity.visualstudio.com/t/boost::spirit-fails-to-compile-with-std/10565526) "boost::spirit fails to compile with /std:c++20 with MSVC 19.34+". It happens with `/std:c++20` and [VS 2022 17.4](https://github.com/microsoft/STL/wiki/VS-2022-Changelog#vs-2022-174) because we implemented [P2520R0](https://wg21.link/P2520R0) "`move_iterator<T*>` should be a random access iterator" (which is a C++23 feature) "downlevel" in C++20 mode (treating it as a defect report, in alignment with libstdc++ and libc++). Both MSVC's compiler and clang-cl repro this with MSVC's STL, and clang-cl emits a more understandable error.  
  
<details><summary>Click to expand clang-cl compiler error (VS 2022 17.10 Preview 1 x64, Clang 17.0.3, Boost 1.84.0):</summary>  
  
```  
C:\Temp>clang-cl /EHsc /nologo /W4 /std:c++20 /MTd /Od /c /I boost_1_84_0\boost_1_84_0 -Xclang -ftemplate-backtrace-limit=0 meow.cpp  
In file included from meow.cpp:4:  
In file included from boost_1_84_0\boost_1_84_0\boost/spirit/home/x3.hpp:67:  
In file included from boost_1_84_0\boost_1_84_0\boost/spirit/home/x3/operator.hpp:10:  
boost_1_84_0\boost_1_84_0\boost/spirit/home/x3/operator/sequence.hpp(63,24): error: no viable overloaded operator[] for  
      type 'const expect_gen'  
   63 |         return left >> expect[right];  
      |                        ^~~~~~ ~~~~~  
C:\Program Files\Microsoft Visual Studio\2022\Preview\VC\Tools\MSVC\14.40.33521\include\concepts(224,11): note: in  
      instantiation of function template specialization  
      'boost::spirit::x3::operator><std::_Vector_iterator<std::_Vector_val<std::_Simple_types<ast::Item>>>,  
      std::_Vector_iterator<std::_Vector_val<std::_Simple_types<ast::Item>>>>' requested here  
  224 |     { __t >  __u } -> _Boolean_testable;  
      |           ^  
C:\Program Files\Microsoft Visual Studio\2022\Preview\VC\Tools\MSVC\14.40.33521\include\concepts(224,7): note: in  
      instantiation of requirement here  
  224 |     { __t >  __u } -> _Boolean_testable;  
      |       ^~~~~~~~~~  
C:\Program Files\Microsoft Visual Studio\2022\Preview\VC\Tools\MSVC\14.40.33521\include\concepts(222,25): note: while  
      substituting template arguments into constraint expression here  
  222 | concept _Half_ordered = requires(const remove_reference_t<_Ty1>& __t, const remove_reference_t<_Ty2>& __u) {  
      |                         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  223 |     { __t <  __u } -> _Boolean_testable;  
      |     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  224 |     { __t >  __u } -> _Boolean_testable;  
      |     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  225 |     { __t <= __u } -> _Boolean_testable;  
      |     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  226 |     { __t >= __u } -> _Boolean_testable;  
      |     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  227 | };  
      | ~  
C:\Program Files\Microsoft Visual Studio\2022\Preview\VC\Tools\MSVC\14.40.33521\include\concepts(233,55): note: while  
      checking the satisfaction of concept  
      '_Half_ordered<std::_Vector_iterator<std::_Vector_val<std::_Simple_types<ast::Item>>>,  
      std::_Vector_iterator<std::_Vector_val<std::_Simple_types<ast::Item>>>>' requested here  
  233 | concept totally_ordered = equality_comparable<_Ty> && _Half_ordered<_Ty, _Ty>;  
      |                                                       ^~~~~~~~~~~~~~~~~~~~~~~  
C:\Program Files\Microsoft Visual Studio\2022\Preview\VC\Tools\MSVC\14.40.33521\include\concepts(233,55): note: while  
      substituting template arguments into constraint expression here  
  233 | concept totally_ordered = equality_comparable<_Ty> && _Half_ordered<_Ty, _Ty>;  
      |                                                       ^~~~~~~~~~~~~~~~~~~~~~~  
C:\Program Files\Microsoft Visual Studio\2022\Preview\VC\Tools\MSVC\14.40.33521\include\xutility(804,72): note: while  
      checking the satisfaction of concept  
      'totally_ordered<std::_Vector_iterator<std::_Vector_val<std::_Simple_types<ast::Item>>>>' requested here  
  804 |     && derived_from<_Iter_concept<_It>, random_access_iterator_tag> && totally_ordered<_It>  
      |                                                                        ^~~~~~~~~~~~~~~~~~~~  
C:\Program Files\Microsoft Visual Studio\2022\Preview\VC\Tools\MSVC\14.40.33521\include\xutility(804,72): note: while  
      substituting template arguments into constraint expression here  
  804 |     && derived_from<_Iter_concept<_It>, random_access_iterator_tag> && totally_ordered<_It>  
      |                                                                        ^~~~~~~~~~~~~~~~~~~~  
C:\Program Files\Microsoft Visual Studio\2022\Preview\VC\Tools\MSVC\14.40.33521\include\xutility(3980,23): note: while  
      checking the satisfaction of concept  
      'random_access_iterator<std::_Vector_iterator<std::_Vector_val<std::_Simple_types<ast::Item>>>>' requested here  
 3980 |         if constexpr (random_access_iterator<_Iter>) {  
      |                       ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
C:\Program Files\Microsoft Visual Studio\2022\Preview\VC\Tools\MSVC\14.40.33521\include\xutility(3992,39): note: in  
      instantiation of member function  
      'std::move_iterator<std::_Vector_iterator<std::_Vector_val<std::_Simple_types<ast::Item>>>>::_Get_iter_concept'  
      requested here  
 3992 |     using iterator_concept = decltype(_Get_iter_concept());  
      |                                       ^  
C:\Program Files\Microsoft Visual Studio\2022\Preview\VC\Tools\MSVC\14.40.33521\include\xutility(4308,46): note: in  
      instantiation of template class  
      'std::move_iterator<std::_Vector_iterator<std::_Vector_val<std::_Simple_types<ast::Item>>>>' requested here  
 4308 | _NODISCARD _CONSTEXPR17 move_iterator<_Iter> make_move_iterator(_Iter _It) noexcept(  
      |                                              ^  
boost_1_84_0\boost_1_84_0\boost/spirit/home/x3/operator/detail/sequence.hpp(383,39): note: in instantiation of function  
      template specialization  
      'std::make_move_iterator<std::_Vector_iterator<std::_Vector_val<std::_Simple_types<ast::Item>>>>' requested here  
  383 |             traits::append(attr, std::make_move_iterator(traits::begin(attr_)),  
      |                                       ^  
boost_1_84_0\boost_1_84_0\boost/spirit/home/x3/operator/detail/sequence.hpp(412,20): note: in instantiation of function  
      template specialization  
      'boost::spirit::x3::detail::parse_into_container_impl<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const  
      char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>,  
      boost::spirit::x3::list<boost::spirit::x3::rule<Item, ast::Item>, boost::spirit::x3::literal_string<const char *,  
      boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::unused_type, const  
      boost::spirit::x3::unused_type>::call<std::_String_const_iterator<std::_String_val<std::_Simple_types<char>>>,  
      std::vector<ast::Item>>' requested here  
  412 |             return call(parser, first, last, context, rcontext, attr  
      |                    ^  
boost_1_84_0\boost_1_84_0\boost/spirit/home/x3/core/detail/parse_into_container.hpp(303,70): note: in instantiation of  
      function template specialization  
      'boost::spirit::x3::detail::parse_into_container_impl<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const  
      char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>,  
      boost::spirit::x3::list<boost::spirit::x3::rule<Item, ast::Item>, boost::spirit::x3::literal_string<const char *,  
      boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::unused_type, const  
      boost::spirit::x3::unused_type>::call<std::_String_const_iterator<std::_String_val<std::_Simple_types<char>>>,  
      std::vector<ast::Item>>' requested here  
  303 |         return parse_into_container_impl<Parser, Context, RContext>::call(  
      |                                                                      ^  
boost_1_84_0\boost_1_84_0\boost/spirit/home/x3/operator/detail/sequence.hpp(282,16): note: in instantiation of function  
      template specialization  
      'boost::spirit::x3::detail::parse_into_container<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const  
      char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>,  
      boost::spirit::x3::list<boost::spirit::x3::rule<Item, ast::Item>, boost::spirit::x3::literal_string<const char *,  
      boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>,  
      std::_String_const_iterator<std::_String_val<std::_Simple_types<char>>>, boost::spirit::x3::unused_type, const  
      boost::spirit::x3::unused_type, std::vector<ast::Item>>' requested here  
  282 |         return parse_into_container(parser, first, last, context, rcontext, attr);  
      |                ^  
boost_1_84_0\boost_1_84_0\boost/spirit/home/x3/operator/detail/sequence.hpp(293,13): note: in instantiation of function  
      template specialization  
      'boost::spirit::x3::detail::parse_sequence_container<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const  
      char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>,  
      boost::spirit::x3::list<boost::spirit::x3::rule<Item, ast::Item>, boost::spirit::x3::literal_string<const char *,  
      boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>,  
      std::_String_const_iterator<std::_String_val<std::_Simple_types<char>>>, boost::spirit::x3::unused_type, const  
      boost::spirit::x3::unused_type, std::vector<ast::Item>>' requested here  
  293 |         if (parse_sequence_container(parser.left, first, last, context, rcontext, attr)  
      |             ^  
boost_1_84_0\boost_1_84_0\boost/spirit/home/x3/operator/sequence.hpp(46,28): note: in instantiation of function template  
      specialization  
      'boost::spirit::x3::detail::parse_sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const  
      char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>,  
      boost::spirit::x3::list<boost::spirit::x3::rule<Item, ast::Item>, boost::spirit::x3::literal_string<const char *,  
      boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::literal_string<const  
      char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>,  
      std::_String_const_iterator<std::_String_val<std::_Simple_types<char>>>, boost::spirit::x3::unused_type, const  
      boost::spirit::x3::unused_type, std::vector<ast::Item>>' requested here  
   46 |             return detail::parse_sequence(*this, first, last, context, rcontext, attr  
      |                            ^  
boost_1_84_0\boost_1_84_0\boost/spirit/home/x3/core/parse.hpp(36,29): note: in instantiation of function template  
      specialization 'boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const  
      char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>,  
      boost::spirit::x3::list<boost::spirit::x3::rule<Item, ast::Item>, boost::spirit::x3::literal_string<const char *,  
      boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::literal_string<const  
      char *, boost::spirit::char_encoding::standard,  
      boost::spirit::x3::unused_type>>::parse<std::_String_const_iterator<std::_String_val<std::_Simple_types<char>>>,  
      boost::spirit::x3::unused_type, const boost::spirit::x3::unused_type, std::vector<ast::Item>>' requested here  
   36 |         return as_parser(p).parse(first, last, unused, unused, attr);  
      |                             ^  
boost_1_84_0\boost_1_84_0\boost/spirit/home/x3/core/parse.hpp(61,16): note: in instantiation of function template  
      specialization  
      'boost::spirit::x3::parse_main<std::_String_const_iterator<std::_String_val<std::_Simple_types<char>>>,  
      boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *,  
      boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>,  
      boost::spirit::x3::list<boost::spirit::x3::rule<Item, ast::Item>, boost::spirit::x3::literal_string<const char *,  
      boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::literal_string<const  
      char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>, std::vector<ast::Item>>'  
      requested here  
   61 |         return parse_main(first, last, p, attr);  
      |                ^  
meow.cpp(35,9): note: in instantiation of function template specialization  
      'boost::spirit::x3::parse<std::_String_const_iterator<std::_String_val<std::_Simple_types<char>>>,  
      boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *,  
      boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>,  
      boost::spirit::x3::list<boost::spirit::x3::rule<Item, ast::Item>, boost::spirit::x3::literal_string<const char *,  
      boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::literal_string<const  
      char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>, std::vector<ast::Item>>'  
      requested here  
   35 |     x3::parse(parsed.cbegin(), parsed.cend(), itemsRule, attribute);  
      |         ^  
boost_1_84_0\boost_1_84_0\boost/spirit/home/x3/directive/expect.hpp(70,9): note: candidate template ignored:  
      substitution failure [with Subject = std::_Vector_iterator<std::_Vector_val<std::_Simple_types<ast::Item>>>]: no  
      type named 'value_type' in  
      'boost::spirit::x3::extension::as_parser<std::_Vector_iterator<std::_Vector_val<std::_Simple_types<ast::Item>>>>'  
   69 |         constexpr expect_directive<typename extension::as_parser<Subject>::value_type>  
      |                                                                            ~~~~~~~~~~  
   70 |         operator[](Subject const& subject) const  
      |         ^  
1 error generated.  
```  
</details>  
  
What's happening is that `boost_1_84_0\boost/spirit/home/x3/operator/detail/sequence.hpp(383,39)` is calling `std::make_move_iterator()` on a `std::vector<ast::Item>::iterator`. Due to P2520R0, `std::move_iterator` has to ask whether the wrapped iterator models `std::random_access_iterator`. Obviously (to humans) this is true for a `std::vector::iterator`, but the library doesn't short-circuit that check, it has to actually evaluate the concept. This has to ask whether the iterator is `std::totally_ordered`, and that ends up asking whether the `std::vector<ast::Item>::iterator`s can be compared with `>`.  
  
:zap: Argument-dependent lookup strikes! :zap:  
===  
  
This ends up considering the following unconstrained `operator>` overload in `boost::spirit::x3`, instantiating it for `std::vector<ast::Item>::iterator`:  
  
https://github.com/boostorg/spirit/blob/f39edf226afc3d7b7d5c84a6c63fe3eaccc1e21b/include/boost/spirit/home/x3/operator/sequence.hpp#L60-L64  
  
The `std::totally_ordered` concept (actually our implementation detail `std::_Half_ordered`, but that doesn't matter) just wants to know whether `vector_iterator > vector_iterator` is well-formed and boolean-testable, but while performing overload resolution, it has to consider Boost.Spirit's unconstrained `operator>`, and the return type is `auto` so it has to instantiate the operator's function body. This causes a hard (non-SFINAE-able) compiler error because whatever `left >> expect[right]` does, it is not prepared for this to be used with `std::vector<ast::Item>::iterator`.  
  
Therefore, this is a bug in Boost.Spirit, not MSVC. (I can't explain why it doesn't repro with libstdc++ and libc++, even with their debug modes - the implementation details of `std::vector` may be relevant.) I believe the fix would involve constraining this `boost::spirit::x3::operator>` overload, but I'm not 100% sure. Hope this helps!

---

## Comment 2

> Username: AlexGuteniev  
> Created at: 2024-02-21 20:42:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/782#issuecomment-1957876745  

> I believe the fix would involve constraining this `boost::spirit::x3::operator>` overload  
  
Implementing own `move_iterator` might be a way forward to avoid using C++20 features (and relying on conditional compilation to support C++17)

---

## Comment 3

> Username: Kojoley  
> Created at: 2024-02-23 01:27:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/782#issuecomment-1960612181  

All operators are constrained by SFINAE on return type except `operator>` , that's inconsistent. But I also thinking about moving types users can inherit from to a separate namespace because SFINAEd-out operators unnecessarily pollutes compiler error messages (which are on their own are hard for users to understand).
