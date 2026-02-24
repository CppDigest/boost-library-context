# #89 - concepts-related error [Closed]

> Username: akrzemi1  
> Created at: 2024-02-02 19:49:49 UTC  
> Updated at: 2024-02-13 16:19:17 UTC  
> Closed at: 2024-02-02 23:45:47 UTC  
> Url: https://github.com/boostorg/parser/issues/89  

I get a strange error message, related to requires-clauses, when I compile my program with clang++   
  
```  
clang version 16.0.6 (Fedora 16.0.6-3.fc38)  
Target: x86_64-redhat-linux-gnu  
Thread model: posix  
```   
  
Here is a Compiler Explorer sample with my code, but the error message does not shw up there.  
https://godbolt.org/z/ohecsYMjs  
  
So instead, let me paste the error message. Maybe you will be able to make some sense from it.  
  
```  
In file included from main3.cpp:1:  
In file included from ../parser/include/boost/parser/parser.hpp:9:  
In file included from ../parser/include/boost/parser/parser_fwd.hpp:10:  
In file included from ../parser/include/boost/parser/error_handling_fwd.hpp:6:  
In file included from ../parser/include/boost/parser/detail/text/transcode_view.hpp:9:  
In file included from ../parser/include/boost/parser/detail/text/transcode_algorithm.hpp:11:  
../parser/include/boost/parser/detail/text/transcode_iterator.hpp:3237:18: error: requires clause differs in template redeclaration  
        requires std::convertible_to<std::iter_value_t<I2>, detail::format_to_type_t<FromFormat2>>  
                 ^  
../parser/include/boost/parser/detail/text/transcode_view.hpp:503:24: note: in instantiation of template class 'boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf32, boost::parser::detail::text::format::utf8, char32_t *>' requested here  
                return utf_iterator<FromFormat, Format, I, S>{first, first, last};  
                       ^  
../parser/include/boost/parser/detail/text/transcode_view.hpp:541:24: note: in instantiation of function template specialization 'boost::parser::detail::text::utf_view<boost::parser::detail::text::format::utf8, std::ranges::subrange<char32_t *, char32_t *, std::ranges::subrange_kind::sized>>::make_begin<boost::parser::detail::text::format::utf32, char32_t *, char32_t *>' requested here  
                return make_begin<from_format>(detail::begin(base_), detail::end(base_));  
                       ^  
/usr/bin/../lib/gcc/x86_64-redhat-linux/13/../../../../include/c++/13/bits/iterator_concepts.h:979:23: note: in instantiation of member function 'boost::parser::detail::text::utf_view<boost::parser::detail::text::format::utf8, std::ranges::subrange<char32_t *, char32_t *, std::ranges::subrange_kind::sized>>::begin' requested here  
          { __decay_copy(__t.begin()) } -> input_or_output_iterator;  
                             ^  
/usr/bin/../lib/gcc/x86_64-redhat-linux/13/../../../../include/c++/13/bits/iterator_concepts.h:979:6: note: in instantiation of requirement here  
          { __decay_copy(__t.begin()) } -> input_or_output_iterator;  
            ^~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/bin/../lib/gcc/x86_64-redhat-linux/13/../../../../include/c++/13/bits/iterator_concepts.h:977:32: note: while substituting template arguments into constraint expression here  
      concept __member_begin = requires(_Tp& __t)  
                               ^~~~~~~~~~~~~~~~~~  
/usr/bin/../lib/gcc/x86_64-redhat-linux/13/../../../../include/c++/13/bits/ranges_base.h:112:50: note: (skipping 21 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
        requires is_array_v<remove_reference_t<_Tp>> || __member_begin<_Tp>  
                                                        ^  
../parser/include/boost/parser/detail/detection.hpp:75:36: note: in instantiation of template type alias 'is_detected' requested here  
    constexpr bool is_detected_v = is_detected<Template, Args...>::value;  
                                   ^  
../parser/include/boost/parser/detail/stl_interfaces/view_adaptor.hpp:58:13: note: in instantiation of variable template specialization 'boost::parser::detail::is_detected_v<boost::parser::detail::stl_interfaces::detail::invocable_expr, boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view, boost::parser::detail::text::format::utf8>, char32_t (&)[1]>' requested here  
            is_detected_v<invocable_expr, F, Args...>;  
            ^  
../parser/include/boost/parser/detail/stl_interfaces/view_adaptor.hpp:199:56: note: in instantiation of variable template specialization 'boost::parser::detail::stl_interfaces::detail::is_invocable_v<boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view, boost::parser::detail::text::format::utf8>, char32_t (&)[1]>' requested here  
            typename Enable = std::enable_if_t<detail::is_invocable_v<D, T>>>  
                                                       ^  
../parser/include/boost/parser/detail/stl_interfaces/view_adaptor.hpp:201:55: note: in instantiation of default argument for 'operator|<char32_t (&)[1]>' required here  
        [[nodiscard]] friend constexpr decltype(auto) operator|(T && t, D && d)  
                                                      ^~~~~~~~~~~~~~~~~~~~~~~~~  
../parser/include/boost/parser/parser.hpp:1285:36: note: while substituting deduced template arguments into function template 'operator|' [with T = char32_t (&)[1], Enable = (no value)]  
                auto const r = cps | text::as_utf8;  
                                   ^  
../parser/include/boost/parser/detail/text/transcode_iterator.hpp:2578:18: note: previous template declaration is here  
        requires std::convertible_to<std::iter_value_t<I>, detail::format_to_type_t<FromFormat>>  
                 ^  
  
```

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-02 23:45:47 UTC  
> Url: https://github.com/boostorg/parser/issues/89#issuecomment-1924911779  

Phil Endicott also reported this same problem.  However, if you look at those two declarations, you will see that the constraints are identical, modulo spelling of the type names.  
  
I think it's related to one or both of these Clang bugs:  
  
https://github.com/llvm/llvm-project/issues/49620  
https://github.com/llvm/llvm-project/issues/61763  
  
There is a workaround.  Define `BOOST_PARSER_DISABLE_CONCEPTS` to be `1`, and you'll get the C++17 SFINAE constraints.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-02-12 19:42:34 UTC  
> Url: https://github.com/boostorg/parser/issues/89#issuecomment-1939430963  

Ok, but maybe Boost.Parser should detect the defective Clang verisons and automatically skip concept definitions?

---

## Comment 3

> Username: tzlaine  
> Created at: 2024-02-13 07:11:23 UTC  
> Url: https://github.com/boostorg/parser/issues/89#issuecomment-1940540570  

Unfortunately, I don't have very good acccess to Clang.  I really just have the one version thay Github actions supports!  If I knew a reliable range of Clang versions that I could work around, I would be happy to do that.

---

## Comment 4

> Username: akrzemi1  
> Created at: 2024-02-13 16:19:16 UTC  
> Url: https://github.com/boostorg/parser/issues/89#issuecomment-1941933880  

Interestingly, it looks like the error disappeared: both in my GodBolt example as well in my local tests.  
Maybe something changed in the implementation.
