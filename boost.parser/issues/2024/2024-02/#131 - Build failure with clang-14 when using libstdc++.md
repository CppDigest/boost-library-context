# #131 - Build failure with clang-14 when using libstdc++ [Closed]

> Username: anarthal  
> Created at: 2024-02-28 14:27:27 UTC  
> Updated at: 2024-04-01 01:19:33 UTC  
> Closed at: 2024-04-01 01:19:33 UTC  
> Url: https://github.com/boostorg/parser/issues/131  

Build can be found here (edited link): https://drone.cpp.al/boostorg/mysql/545/13/3 (the log is extremely verbose because of warnings). I've isolated the error log to this:  
  
```  
In file included from libs/mysql/test/parser.cpp:1:  
In file included from ./boost/parser/parser.hpp:9:  
In file included from ./boost/parser/parser_fwd.hpp:9:  
In file included from ./boost/parser/config.hpp:12:  
In file included from /usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/iterator:61:  
In file included from /usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/stl_iterator_base_types.h:71:  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:979:13: error: no matching function for call to '__begin'  
        = decltype(ranges::__cust_access::__begin(std::declval<_Tp&>()));  
                   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_base.h:590:5: note: in instantiation of template type alias '__range_iter_t' requested here  
    using iterator_t = std::__detail::__range_iter_t<_Tp>;  
    ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_util.h:98:43: note: in instantiation of template type alias 'iterator_t' requested here  
      data() requires contiguous_iterator<iterator_t<_Derived>>  
                                          ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_util.h:203:29: note: in instantiation of template class 'std::ranges::view_interface<std::ranges::subrange<char32_t *, char32_t *, std::ranges::subrange_kind::sized>>' requested here  
    class subrange : public view_interface<subrange<_It, _Sent, _Kind>>  
                            ^  
./boost/parser/detail/text/transcode_view.hpp:738:28: note: in instantiation of template class 'std::ranges::subrange<char32_t *, char32_t *, std::ranges::subrange_kind::sized>' requested here  
                    return BOOST_PARSER_DETAIL_TEXT_SUBRANGE(unpacked.first, unpacked.last);  
                           ^  
./boost/parser/detail/text/config.hpp:40:47: note: expanded from macro 'BOOST_PARSER_DETAIL_TEXT_SUBRANGE'  
#    define BOOST_PARSER_DETAIL_TEXT_SUBRANGE std::ranges::subrange  
                                              ^  
./boost/parser/detail/text/transcode_view.hpp:752:49: note: in instantiation of function template specialization 'boost::parser::detail::text::detail::unpack_range<char32_t (&)[1]>' requested here  
        using unpacked_range = decltype(detail::unpack_range(std::declval<R>()));  
                                                ^  
./boost/parser/detail/text/transcode_view.hpp:761:40: note: (skipping 7 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
                          can_utf_view<unpacked_range<R>, View>) ||  
                                       ^  
./boost/parser/detail/detection.hpp:75:36: note: in instantiation of template type alias 'is_detected' requested here  
    constexpr bool is_detected_v = is_detected<Template, Args...>::value;  
                                   ^  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:58:13: note: in instantiation of variable template specialization 'boost::parser::detail::is_detected_v<boost::parser::detail::stl_interfaces::detail::invocable_expr, boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view, boost::parser::detail::text::format::utf8>, char32_t (&)[1]>' requested here  
            is_detected_v<invocable_expr, F, Args...>;  
            ^  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:199:56: note: in instantiation of variable template specialization 'boost::parser::detail::stl_interfaces::detail::is_invocable_v<boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view, boost::parser::detail::text::format::utf8>, char32_t (&)[1]>' requested here  
            typename Enable = std::enable_if_t<detail::is_invocable_v<D, T>>>  
                                                       ^  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:201:55: note: in instantiation of default argument for 'operator|<char32_t (&)[1]>' required here  
        [[nodiscard]] friend constexpr decltype(auto) operator|(T && t, D && d)  
                                                      ^~~~~~~~~~~~~~~~~~~~~~~~~  
./boost/parser/parser.hpp:1285:36: note: while substituting deduced template arguments into function template 'operator|' [with T = char32_t (&)[1], Enable = (no value)]  
                auto const r = cps | text::as_utf8;  
                                   ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:963:7: note: candidate template ignored: constraints not satisfied [with _Tp = std::ranges::subrange<char32_t *, char32_t *, std::ranges::subrange_kind::sized>]  
      __begin(_Tp& __t)  
      ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:961:16: note: because 'is_array_v<std::ranges::subrange<char32_t *, char32_t *, std::ranges::subrange_kind::sized> >' evaluated to false  
      requires is_array_v<_Tp> || __member_begin<_Tp&> || __adl_begin<_Tp&>  
               ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:961:35: note: and 'std::ranges::subrange<char32_t *, char32_t *, std::ranges::subrange_kind::sized> &' does not satisfy '__member_begin'  
      requires is_array_v<_Tp> || __member_begin<_Tp&> || __adl_begin<_Tp&>  
                                  ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:944:23: note: because '__decay_copy(__t.begin())' would be invalid: no member named 'begin' in 'std::ranges::subrange<char32_t *, char32_t *, std::ranges::subrange_kind::sized>'  
          { __decay_copy(__t.begin()) } -> input_or_output_iterator;  
                             ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:961:59: note: and 'std::ranges::subrange<char32_t *, char32_t *, std::ranges::subrange_kind::sized> &' does not satisfy '__adl_begin'  
      requires is_array_v<_Tp> || __member_begin<_Tp&> || __adl_begin<_Tp&>  
                                                          ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:955:19: note: because '__decay_copy(begin(__t))' would be invalid: call to deleted function 'begin'  
          { __decay_copy(begin(__t)) } -> input_or_output_iterator;  
                         ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:979:13: error: no matching function for call to '__begin'  
        = decltype(ranges::__cust_access::__begin(std::declval<_Tp&>()));  
                   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_base.h:590:5: note: in instantiation of template type alias '__range_iter_t' requested here  
    using iterator_t = std::__detail::__range_iter_t<_Tp>;  
    ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_util.h:104:25: note: in instantiation of template type alias 'iterator_t' requested here  
        && contiguous_iterator<iterator_t<const _Derived>>  
                               ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_util.h:203:29: note: in instantiation of template class 'std::ranges::view_interface<std::ranges::subrange<char32_t *, char32_t *, std::ranges::subrange_kind::sized>>' requested here  
    class subrange : public view_interface<subrange<_It, _Sent, _Kind>>  
                            ^  
./boost/parser/detail/text/transcode_view.hpp:738:28: note: in instantiation of template class 'std::ranges::subrange<char32_t *, char32_t *, std::ranges::subrange_kind::sized>' requested here  
                    return BOOST_PARSER_DETAIL_TEXT_SUBRANGE(unpacked.first, unpacked.last);  
                           ^  
./boost/parser/detail/text/config.hpp:40:47: note: expanded from macro 'BOOST_PARSER_DETAIL_TEXT_SUBRANGE'  
#    define BOOST_PARSER_DETAIL_TEXT_SUBRANGE std::ranges::subrange  
                                              ^  
./boost/parser/detail/text/transcode_view.hpp:752:49: note: in instantiation of function template specialization 'boost::parser::detail::text::detail::unpack_range<char32_t (&)[1]>' requested here  
        using unpacked_range = decltype(detail::unpack_range(std::declval<R>()));  
                                                ^  
./boost/parser/detail/text/transcode_view.hpp:761:40: note: (skipping 7 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
                          can_utf_view<unpacked_range<R>, View>) ||  
                                       ^  
./boost/parser/detail/detection.hpp:75:36: note: in instantiation of template type alias 'is_detected' requested here  
    constexpr bool is_detected_v = is_detected<Template, Args...>::value;  
                                   ^  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:58:13: note: in instantiation of variable template specialization 'boost::parser::detail::is_detected_v<boost::parser::detail::stl_interfaces::detail::invocable_expr, boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view, boost::parser::detail::text::format::utf8>, char32_t (&)[1]>' requested here  
            is_detected_v<invocable_expr, F, Args...>;  
            ^  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:199:56: note: in instantiation of variable template specialization 'boost::parser::detail::stl_interfaces::detail::is_invocable_v<boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view, boost::parser::detail::text::format::utf8>, char32_t (&)[1]>' requested here  
            typename Enable = std::enable_if_t<detail::is_invocable_v<D, T>>>  
                                                       ^  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:201:55: note: in instantiation of default argument for 'operator|<char32_t (&)[1]>' required here  
        [[nodiscard]] friend constexpr decltype(auto) operator|(T && t, D && d)  
                                                      ^~~~~~~~~~~~~~~~~~~~~~~~~  
./boost/parser/parser.hpp:1285:36: note: while substituting deduced template arguments into function template 'operator|' [with T = char32_t (&)[1], Enable = (no value)]  
                auto const r = cps | text::as_utf8;  
                                   ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:963:7: note: candidate template ignored: constraints not satisfied [with _Tp = const std::ranges::subrange<char32_t *, char32_t *, std::ranges::subrange_kind::sized>]  
      __begin(_Tp& __t)  
      ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:961:16: note: because 'is_array_v<const std::ranges::subrange<char32_t *, char32_t *, std::ranges::subrange_kind::sized> >' evaluated to false  
      requires is_array_v<_Tp> || __member_begin<_Tp&> || __adl_begin<_Tp&>  
               ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:961:35: note: and 'const std::ranges::subrange<char32_t *, char32_t *, std::ranges::subrange_kind::sized> &' does not satisfy '__member_begin'  
      requires is_array_v<_Tp> || __member_begin<_Tp&> || __adl_begin<_Tp&>  
                                  ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:944:23: note: because '__decay_copy(__t.begin())' would be invalid: no member named 'begin' in 'std::ranges::subrange<char32_t *, char32_t *, std::ranges::subrange_kind::sized>'  
          { __decay_copy(__t.begin()) } -> input_or_output_iterator;  
                             ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:961:59: note: and 'const std::ranges::subrange<char32_t *, char32_t *, std::ranges::subrange_kind::sized> &' does not satisfy '__adl_begin'  
      requires is_array_v<_Tp> || __member_begin<_Tp&> || __adl_begin<_Tp&>  
                                                          ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:955:19: note: because '__decay_copy(begin(__t))' would be invalid: call to deleted function 'begin'  
          { __decay_copy(begin(__t)) } -> input_or_output_iterator;  
                         ^  
In file included from libs/mysql/test/parser.cpp:1:  
In file included from ./boost/parser/parser.hpp:9:  
In file included from ./boost/parser/parser_fwd.hpp:10:  
In file included from ./boost/parser/error_handling_fwd.hpp:6:  
In file included from ./boost/parser/detail/text/transcode_view.hpp:9:  
In file included from ./boost/parser/detail/text/transcode_algorithm.hpp:10:  
In file included from ./boost/parser/detail/text/in_out_result.hpp:27:  
In file included from /usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/ranges:46:  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_util.h:110:24: error: constraints not satisfied for alias template 'sentinel_t' [with _Range = std::ranges::subrange<char32_t *, char32_t *, std::ranges::subrange_kind::sized>]  
        && sized_sentinel_for<sentinel_t<_Derived>, iterator_t<_Derived>>  
                              ^~~~~~~~~~~~~~~~~~~~  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_util.h:203:29: note: in instantiation of template class 'std::ranges::view_interface<std::ranges::subrange<char32_t *, char32_t *, std::ranges::subrange_kind::sized>>' requested here  
    class subrange : public view_interface<subrange<_It, _Sent, _Kind>>  
                            ^  
./boost/parser/detail/text/transcode_view.hpp:738:28: note: in instantiation of template class 'std::ranges::subrange<char32_t *, char32_t *, std::ranges::subrange_kind::sized>' requested here  
                    return BOOST_PARSER_DETAIL_TEXT_SUBRANGE(unpacked.first, unpacked.last);  
                           ^  
./boost/parser/detail/text/config.hpp:40:47: note: expanded from macro 'BOOST_PARSER_DETAIL_TEXT_SUBRANGE'  
#    define BOOST_PARSER_DETAIL_TEXT_SUBRANGE std::ranges::subrange  
                                              ^  
./boost/parser/detail/text/transcode_view.hpp:752:49: note: in instantiation of function template specialization 'boost::parser::detail::text::detail::unpack_range<char32_t (&)[1]>' requested here  
        using unpacked_range = decltype(detail::unpack_range(std::declval<R>()));  
                                                ^  
./boost/parser/detail/text/transcode_view.hpp:761:40: note: in instantiation of template type alias 'unpacked_range' requested here  
                          can_utf_view<unpacked_range<R>, View>) ||  
                                       ^  
./boost/parser/detail/text/transcode_view.hpp:761:27: note: while substituting template arguments into constraint expression here  
                          can_utf_view<unpacked_range<R>, View>) ||  
                          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:55:22: note: (skipping 5 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
            decltype(std::declval<F>()(std::declval<Args>()...));  
                     ^  
./boost/parser/detail/detection.hpp:75:36: note: in instantiation of template type alias 'is_detected' requested here  
    constexpr bool is_detected_v = is_detected<Template, Args...>::value;  
                                   ^  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:58:13: note: in instantiation of variable template specialization 'boost::parser::detail::is_detected_v<boost::parser::detail::stl_interfaces::detail::invocable_expr, boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view, boost::parser::detail::text::format::utf8>, char32_t (&)[1]>' requested here  
            is_detected_v<invocable_expr, F, Args...>;  
            ^  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:199:56: note: in instantiation of variable template specialization 'boost::parser::detail::stl_interfaces::detail::is_invocable_v<boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view, boost::parser::detail::text::format::utf8>, char32_t (&)[1]>' requested here  
            typename Enable = std::enable_if_t<detail::is_invocable_v<D, T>>>  
                                                       ^  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:201:55: note: in instantiation of default argument for 'operator|<char32_t (&)[1]>' required here  
        [[nodiscard]] friend constexpr decltype(auto) operator|(T && t, D && d)  
                                                      ^~~~~~~~~~~~~~~~~~~~~~~~~  
./boost/parser/parser.hpp:1285:36: note: while substituting deduced template arguments into function template 'operator|' [with T = char32_t (&)[1], Enable = (no value)]  
                auto const r = cps | text::as_utf8;  
                                   ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_base.h:592:12: note: because 'std::ranges::subrange<char32_t *, char32_t *, std::ranges::subrange_kind::sized>' does not satisfy 'range'  
  template<range _Range>  
           ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_base.h:580:2: note: because 'ranges::begin(__t)' would be invalid: no matching function for call to object of type 'const __cust_access::_Begin'  
        ranges::begin(__t);  
        ^  
In file included from libs/mysql/test/parser.cpp:1:  
In file included from ./boost/parser/parser.hpp:9:  
In file included from ./boost/parser/parser_fwd.hpp:10:  
In file included from ./boost/parser/error_handling_fwd.hpp:6:  
In file included from ./boost/parser/detail/text/transcode_view.hpp:9:  
In file included from ./boost/parser/detail/text/transcode_algorithm.hpp:10:  
In file included from ./boost/parser/detail/text/in_out_result.hpp:27:  
In file included from /usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/ranges:46:  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_util.h:116:24: error: constraints not satisfied for alias template 'sentinel_t' [with _Range = const std::ranges::subrange<char32_t *, char32_t *, std::ranges::subrange_kind::sized>]  
        && sized_sentinel_for<sentinel_t<const _Derived>,  
                              ^~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_base.h:592:12: note: because 'const std::ranges::subrange<char32_t *, char32_t *, std::ranges::subrange_kind::sized>' does not satisfy 'range'  
  template<range _Range>  
           ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_base.h:580:2: note: because 'ranges::begin(__t)' would be invalid: no matching function for call to object of type 'const __cust_access::_Begin'  
        ranges::begin(__t);  
        ^  
In file included from libs/mysql/test/parser.cpp:1:  
./boost/parser/parser.hpp:1285:36: error: invalid operands to binary expression ('char32_t[1]' and 'const detail::as_utf_impl<boost::parser::detail::text::utf8_view, format::utf8>')  
                auto const r = cps | text::as_utf8;  
                               ~~~ ^ ~~~~~~~~~~~~~  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:201:55: note: candidate template ignored: requirement 'detail::is_invocable_v<boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view, boost::parser::detail::text::format::utf8>, char32_t (&)[1]>' was not satisfied [with T = char32_t (&)[1]]  
        [[nodiscard]] friend constexpr decltype(auto) operator|(T && t, D && d)  
                                                      ^  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:216:9: note: candidate template ignored: requirement 'detail::is_invocable_v<const boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view, boost::parser::detail::text::format::utf8> &, char32_t (&)[1]>' was not satisfied [with T = char32_t (&)[1]]  
        operator|(T && t, D const & d)  
        ^  
In file included from libs/mysql/test/parser.cpp:1:  
./boost/parser/parser.hpp:1752:36: error: invalid operands to binary expression ('char32_t[1]' and 'const detail::as_utf_impl<boost::parser::detail::text::utf8_view, format::utf8>')  
                auto const r = cps | text::as_utf8;  
                               ~~~ ^ ~~~~~~~~~~~~~  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:201:55: note: candidate template ignored: requirement 'detail::is_invocable_v<boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view, boost::parser::detail::text::format::utf8>, char32_t (&)[1]>' was not satisfied [with T = char32_t (&)[1]]  
        [[nodiscard]] friend constexpr decltype(auto) operator|(T && t, D && d)  
                                                      ^  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:216:9: note: candidate template ignored: requirement 'detail::is_invocable_v<const boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view, boost::parser::detail::text::format::utf8> &, char32_t (&)[1]>' was not satisfied [with T = char32_t (&)[1]]  
        operator|(T && t, D const & d)  
        ^  
In file included from libs/mysql/test/parser.cpp:1:  
In file included from ./boost/parser/parser.hpp:9:  
In file included from ./boost/parser/parser_fwd.hpp:9:  
In file included from ./boost/parser/config.hpp:12:  
In file included from /usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/iterator:61:  
In file included from /usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/stl_iterator_base_types.h:71:  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:979:13: error: no matching function for call to '__begin'  
        = decltype(ranges::__cust_access::__begin(std::declval<_Tp&>()));  
                   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_base.h:590:5: note: in instantiation of template type alias '__range_iter_t' requested here  
    using iterator_t = std::__detail::__range_iter_t<_Tp>;  
    ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_util.h:98:43: note: in instantiation of template type alias 'iterator_t' requested here  
      data() requires contiguous_iterator<iterator_t<_Derived>>  
                                          ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/ranges:1048:29: note: in instantiation of template class 'std::ranges::view_interface<std::ranges::ref_view<std::array<char32_t, 1>>>' requested here  
    class ref_view : public view_interface<ref_view<_Range>>  
                            ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/ranges:1104:38: note: in instantiation of template class 'std::ranges::ref_view<std::array<char32_t, 1>>' requested here  
        concept __can_ref_view = requires { ref_view{std::declval<_Range>()}; };  
                                            ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/ranges:1104:38: note: in instantiation of requirement here  
        concept __can_ref_view = requires { ref_view{std::declval<_Range>()}; };  
                                            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/ranges:1104:27: note: (skipping 17 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
        concept __can_ref_view = requires { ref_view{std::declval<_Range>()}; };  
                                 ^  
./boost/parser/detail/detection.hpp:75:36: note: in instantiation of template type alias 'is_detected' requested here  
    constexpr bool is_detected_v = is_detected<Template, Args...>::value;  
                                   ^  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:58:13: note: in instantiation of variable template specialization 'boost::parser::detail::is_detected_v<boost::parser::detail::stl_interfaces::detail::invocable_expr, boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view, boost::parser::detail::text::format::utf8>, std::array<char32_t, 1> &>' requested here  
            is_detected_v<invocable_expr, F, Args...>;  
            ^  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:199:56: note: in instantiation of variable template specialization 'boost::parser::detail::stl_interfaces::detail::is_invocable_v<boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view, boost::parser::detail::text::format::utf8>, std::array<char32_t, 1> &>' requested here  
            typename Enable = std::enable_if_t<detail::is_invocable_v<D, T>>>  
                                                       ^  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:201:55: note: in instantiation of default argument for 'operator|<std::array<char32_t, 1> &>' required here  
        [[nodiscard]] friend constexpr decltype(auto) operator|(T && t, D && d)  
                                                      ^~~~~~~~~~~~~~~~~~~~~~~~~  
./boost/parser/detail/printing_impl.hpp:462:32: note: while substituting deduced template arguments into function template 'operator|' [with T = std::array<char32_t, 1> &, Enable = (no value)]  
            auto const r = cps | text::as_utf8;  
                               ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:963:7: note: candidate template ignored: constraints not satisfied [with _Tp = std::ranges::ref_view<std::array<char32_t, 1>>]  
      __begin(_Tp& __t)  
      ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:961:16: note: because 'is_array_v<std::ranges::ref_view<std::array<char32_t, 1> > >' evaluated to false  
      requires is_array_v<_Tp> || __member_begin<_Tp&> || __adl_begin<_Tp&>  
               ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:961:35: note: and 'std::ranges::ref_view<std::array<char32_t, 1>> &' does not satisfy '__member_begin'  
      requires is_array_v<_Tp> || __member_begin<_Tp&> || __adl_begin<_Tp&>  
                                  ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:944:23: note: because '__decay_copy(__t.begin())' would be invalid: no member named 'begin' in 'std::ranges::ref_view<std::array<char32_t, 1>>'  
          { __decay_copy(__t.begin()) } -> input_or_output_iterator;  
                             ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:961:59: note: and 'std::ranges::ref_view<std::array<char32_t, 1>> &' does not satisfy '__adl_begin'  
      requires is_array_v<_Tp> || __member_begin<_Tp&> || __adl_begin<_Tp&>  
                                                          ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:955:19: note: because '__decay_copy(begin(__t))' would be invalid: call to deleted function 'begin'  
          { __decay_copy(begin(__t)) } -> input_or_output_iterator;  
                         ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:979:13: error: no matching function for call to '__begin'  
        = decltype(ranges::__cust_access::__begin(std::declval<_Tp&>()));  
                   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_base.h:590:5: note: in instantiation of template type alias '__range_iter_t' requested here  
    using iterator_t = std::__detail::__range_iter_t<_Tp>;  
    ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_util.h:104:25: note: in instantiation of template type alias 'iterator_t' requested here  
        && contiguous_iterator<iterator_t<const _Derived>>  
                               ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/ranges:1048:29: note: in instantiation of template class 'std::ranges::view_interface<std::ranges::ref_view<std::array<char32_t, 1>>>' requested here  
    class ref_view : public view_interface<ref_view<_Range>>  
                            ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/ranges:1104:38: note: in instantiation of template class 'std::ranges::ref_view<std::array<char32_t, 1>>' requested here  
        concept __can_ref_view = requires { ref_view{std::declval<_Range>()}; };  
                                            ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/ranges:1104:38: note: in instantiation of requirement here  
        concept __can_ref_view = requires { ref_view{std::declval<_Range>()}; };  
                                            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/ranges:1104:27: note: (skipping 17 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
        concept __can_ref_view = requires { ref_view{std::declval<_Range>()}; };  
                                 ^  
./boost/parser/detail/detection.hpp:75:36: note: in instantiation of template type alias 'is_detected' requested here  
    constexpr bool is_detected_v = is_detected<Template, Args...>::value;  
                                   ^  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:58:13: note: in instantiation of variable template specialization 'boost::parser::detail::is_detected_v<boost::parser::detail::stl_interfaces::detail::invocable_expr, boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view, boost::parser::detail::text::format::utf8>, std::array<char32_t, 1> &>' requested here  
            is_detected_v<invocable_expr, F, Args...>;  
            ^  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:199:56: note: in instantiation of variable template specialization 'boost::parser::detail::stl_interfaces::detail::is_invocable_v<boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view, boost::parser::detail::text::format::utf8>, std::array<char32_t, 1> &>' requested here  
            typename Enable = std::enable_if_t<detail::is_invocable_v<D, T>>>  
                                                       ^  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:201:55: note: in instantiation of default argument for 'operator|<std::array<char32_t, 1> &>' required here  
        [[nodiscard]] friend constexpr decltype(auto) operator|(T && t, D && d)  
                                                      ^~~~~~~~~~~~~~~~~~~~~~~~~  
./boost/parser/detail/printing_impl.hpp:462:32: note: while substituting deduced template arguments into function template 'operator|' [with T = std::array<char32_t, 1> &, Enable = (no value)]  
            auto const r = cps | text::as_utf8;  
                               ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:963:7: note: candidate template ignored: constraints not satisfied [with _Tp = const std::ranges::ref_view<std::array<char32_t, 1>>]  
      __begin(_Tp& __t)  
      ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:961:16: note: because 'is_array_v<const std::ranges::ref_view<std::array<char32_t, 1> > >' evaluated to false  
      requires is_array_v<_Tp> || __member_begin<_Tp&> || __adl_begin<_Tp&>  
               ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:961:35: note: and 'const std::ranges::ref_view<std::array<char32_t, 1>> &' does not satisfy '__member_begin'  
      requires is_array_v<_Tp> || __member_begin<_Tp&> || __adl_begin<_Tp&>  
                                  ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:944:23: note: because '__decay_copy(__t.begin())' would be invalid: no member named 'begin' in 'std::ranges::ref_view<std::array<char32_t, 1>>'  
          { __decay_copy(__t.begin()) } -> input_or_output_iterator;  
                             ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:961:59: note: and 'const std::ranges::ref_view<std::array<char32_t, 1>> &' does not satisfy '__adl_begin'  
      requires is_array_v<_Tp> || __member_begin<_Tp&> || __adl_begin<_Tp&>  
                                                          ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/iterator_concepts.h:955:19: note: because '__decay_copy(begin(__t))' would be invalid: call to deleted function 'begin'  
          { __decay_copy(begin(__t)) } -> input_or_output_iterator;  
                         ^  
In file included from libs/mysql/test/parser.cpp:1:  
In file included from ./boost/parser/parser.hpp:9:  
In file included from ./boost/parser/parser_fwd.hpp:10:  
In file included from ./boost/parser/error_handling_fwd.hpp:6:  
In file included from ./boost/parser/detail/text/transcode_view.hpp:9:  
In file included from ./boost/parser/detail/text/transcode_algorithm.hpp:10:  
In file included from ./boost/parser/detail/text/in_out_result.hpp:27:  
In file included from /usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/ranges:46:  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_util.h:110:24: error: constraints not satisfied for alias template 'sentinel_t' [with _Range = std::ranges::ref_view<std::array<char32_t, 1>>]  
        && sized_sentinel_for<sentinel_t<_Derived>, iterator_t<_Derived>>  
                              ^~~~~~~~~~~~~~~~~~~~  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/ranges:1048:29: note: in instantiation of template class 'std::ranges::view_interface<std::ranges::ref_view<std::array<char32_t, 1>>>' requested here  
    class ref_view : public view_interface<ref_view<_Range>>  
                            ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/ranges:1104:38: note: in instantiation of template class 'std::ranges::ref_view<std::array<char32_t, 1>>' requested here  
        concept __can_ref_view = requires { ref_view{std::declval<_Range>()}; };  
                                            ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/ranges:1104:38: note: in instantiation of requirement here  
        concept __can_ref_view = requires { ref_view{std::declval<_Range>()}; };  
                                            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/ranges:1104:27: note: while substituting template arguments into constraint expression here  
        concept __can_ref_view = requires { ref_view{std::declval<_Range>()}; };  
                                 ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/ranges:1114:7: note: while checking the satisfaction of concept '__can_ref_view<std::array<char32_t, 1> &>' requested here  
          || __detail::__can_ref_view<_Range>  
             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/ranges:1114:17: note: (skipping 15 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
          || __detail::__can_ref_view<_Range>  
                       ^  
./boost/parser/detail/detection.hpp:75:36: note: in instantiation of template type alias 'is_detected' requested here  
    constexpr bool is_detected_v = is_detected<Template, Args...>::value;  
                                   ^  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:58:13: note: in instantiation of variable template specialization 'boost::parser::detail::is_detected_v<boost::parser::detail::stl_interfaces::detail::invocable_expr, boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view, boost::parser::detail::text::format::utf8>, std::array<char32_t, 1> &>' requested here  
            is_detected_v<invocable_expr, F, Args...>;  
            ^  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:199:56: note: in instantiation of variable template specialization 'boost::parser::detail::stl_interfaces::detail::is_invocable_v<boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view, boost::parser::detail::text::format::utf8>, std::array<char32_t, 1> &>' requested here  
            typename Enable = std::enable_if_t<detail::is_invocable_v<D, T>>>  
                                                       ^  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:201:55: note: in instantiation of default argument for 'operator|<std::array<char32_t, 1> &>' required here  
        [[nodiscard]] friend constexpr decltype(auto) operator|(T && t, D && d)  
                                                      ^~~~~~~~~~~~~~~~~~~~~~~~~  
./boost/parser/detail/printing_impl.hpp:462:32: note: while substituting deduced template arguments into function template 'operator|' [with T = std::array<char32_t, 1> &, Enable = (no value)]  
            auto const r = cps | text::as_utf8;  
                               ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_base.h:592:12: note: because 'std::ranges::ref_view<std::array<char32_t, 1>>' does not satisfy 'range'  
  template<range _Range>  
           ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_base.h:580:2: note: because 'ranges::begin(__t)' would be invalid: no matching function for call to object of type 'const __cust_access::_Begin'  
        ranges::begin(__t);  
        ^  
In file included from libs/mysql/test/parser.cpp:1:  
In file included from ./boost/parser/parser.hpp:9:  
In file included from ./boost/parser/parser_fwd.hpp:10:  
In file included from ./boost/parser/error_handling_fwd.hpp:6:  
In file included from ./boost/parser/detail/text/transcode_view.hpp:9:  
In file included from ./boost/parser/detail/text/transcode_algorithm.hpp:10:  
In file included from ./boost/parser/detail/text/in_out_result.hpp:27:  
In file included from /usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/ranges:46:  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_util.h:116:24: error: constraints not satisfied for alias template 'sentinel_t' [with _Range = const std::ranges::ref_view<std::array<char32_t, 1>>]  
        && sized_sentinel_for<sentinel_t<const _Derived>,  
                              ^~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_base.h:592:12: note: because 'const std::ranges::ref_view<std::array<char32_t, 1>>' does not satisfy 'range'  
  template<range _Range>  
           ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ranges_base.h:580:2: note: because 'ranges::begin(__t)' would be invalid: no matching function for call to object of type 'const __cust_access::_Begin'  
        ranges::begin(__t);  
        ^  
In file included from libs/mysql/test/parser.cpp:1:  
In file included from ./boost/parser/parser.hpp:7517:  
./boost/parser/detail/printing_impl.hpp:462:32: error: invalid operands to binary expression ('std::array<char32_t, 1>' and 'const detail::as_utf_impl<boost::parser::detail::text::utf8_view, format::utf8>')  
            auto const r = cps | text::as_utf8;  
                           ~~~ ^ ~~~~~~~~~~~~~  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ios_base.h:87:3: note: candidate function not viable: no known conversion from 'std::array<char32_t, 1>' to 'std::_Ios_Fmtflags' for 1st argument  
  operator|(_Ios_Fmtflags __a, _Ios_Fmtflags __b)  
  ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ios_base.h:129:3: note: candidate function not viable: no known conversion from 'std::array<char32_t, 1>' to 'std::_Ios_Openmode' for 1st argument  
  operator|(_Ios_Openmode __a, _Ios_Openmode __b)  
  ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/ios_base.h:169:3: note: candidate function not viable: no known conversion from 'std::array<char32_t, 1>' to 'std::_Ios_Iostate' for 1st argument  
  operator|(_Ios_Iostate __a, _Ios_Iostate __b)  
  ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/bits/atomic_base.h:98:3: note: candidate function not viable: no known conversion from 'std::array<char32_t, 1>' to 'std::memory_order' for 1st argument  
  operator|(memory_order __m, __memory_order_modifier __mod)  
  ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/cstddef:137:3: note: candidate function not viable: no known conversion from 'std::array<char32_t, 1>' to 'std::byte' for 1st argument  
  operator|(byte __l, byte __r) noexcept  
  ^  
./boost/parser/parser.hpp:7355:20: note: candidate template ignored: could not match 'parser_interface' against 'as_utf_impl'  
    constexpr auto operator|(char c, parser_interface<Parser> rhs) noexcept  
                   ^  
./boost/parser/parser.hpp:7366:20: note: candidate template ignored: could not match 'parser_interface' against 'as_utf_impl'  
    constexpr auto operator|(char32_t c, parser_interface<Parser> rhs) noexcept  
                   ^  
./boost/parser/parser.hpp:7384:20: note: candidate template ignored: could not match 'parser_interface' against 'as_utf_impl'  
    constexpr auto operator|(R && r, parser_interface<Parser> rhs) noexcept  
                   ^  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:201:55: note: candidate template ignored: requirement 'detail::is_invocable_v<boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view, boost::parser::detail::text::format::utf8>, std::array<char32_t, 1> &>' was not satisfied [with T = std::array<char32_t, 1> &]  
        [[nodiscard]] friend constexpr decltype(auto) operator|(T && t, D && d)  
                                                      ^  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:216:9: note: candidate template ignored: requirement 'detail::is_invocable_v<const boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view, boost::parser::detail::text::format::utf8> &, std::array<char32_t, 1> &>' was not satisfied [with T = std::array<char32_t, 1> &]  
        operator|(T && t, D const & d)  
        ^  
11 errors generated.  
  
    "clang++"   -std=c++20 -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden --target=x86_64-pc-linux  -DBOOST_ALL_NO_LIB=1   -I"."  -c -o "bin.v2/libs/mysql/test/clng-lnx-14/dbg/cxstd-20-iso/thrd-mlt/vsblt-hdn/parser.o" "libs/mysql/test/parser.cpp"  
  
...failed clang-linux.compile.c++ bin.v2/libs/mysql/test/clng-lnx-14/dbg/cxstd-20-iso/thrd-mlt/vsblt-hdn/parser.o...  
```  
  
I've reproduced it with an executable that just includes `<boost/parser/parser.hpp>` and a trivial main.  
  
I think you're hitting these:  
* https://github.com/llvm/llvm-project/issues/52696  
* https://github.com/llvm/llvm-project/issues/44178

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-29 05:25:29 UTC  
> Updated at: 2024-03-30 19:00:31 UTC  
> Url: https://github.com/boostorg/parser/issues/131#issuecomment-1970430077  

Yep.  Looks like it.  I guess I'll need to disable the use of concepts in Clang < 16.
