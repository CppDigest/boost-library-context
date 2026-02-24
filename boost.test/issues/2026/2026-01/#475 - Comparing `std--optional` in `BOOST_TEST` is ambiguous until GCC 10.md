# #475 - Comparing `std::optional` in `BOOST_TEST` is ambiguous until GCC 10 [Closed]

> Username: Flamefire  
> Created at: 2026-01-23 11:22:39 UTC  
> Updated at: 2026-01-29 23:59:04 UTC  
> Closed at: 2026-01-29 23:59:04 UTC  
> Url: https://github.com/boostorg/test/issues/475  

See https://godbolt.org/z/EEMz865o4  
  
```  
#include <optional>  
#include <boost/test/unit_test.hpp>  
  
BOOST_TEST_DONT_PRINT_LOG_VALUE(std::optional<int>)  
  
void foo() {  
    std::optional<int> a,b;  
    BOOST_TEST(a==b);  
}  
```  
  
Yields:  
  
```  
<source>:8:5: error: request for member 'evaluate' in 'std::operator==<int, boost::test_tools::assertion::value_expr<std::optional<int>&> >(boost::test_tools::assertion::seed::operator->*(T&&) const [with T = std::optional<int>&](a), b)', which is of non-class type 'std::__optional_relop_t<boost::test_tools::assertion::binary_expr<boost::test_tools::assertion::value_expr<std::optional<int>&>, int, boost::test_tools::assertion::op::EQ<std::optional<int>, int, void> > >' {aka 'bool'}  
    8 |     BOOST_TEST(a==b);  
      |     ^~~~~~~~~~  
```  
  
It works in GCC 10 and up  
  
<details>  
<summary>More of the error output</summary>  
<source>: In function 'void foo()':  
<source>:8:19: warning: ISO C++ says that these are ambiguous, even though the worst conversion for the first is better than the worst conversion for the second:  
    8 |     BOOST_TEST(a==b);  
      |                   ^  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1101:5: note: candidate 1: 'constexpr std::__optional_relop_t<decltype ((declval<_Up>() == declval<_Tp>()))> std::operator==(const _Up&, const std::optional<_Tp>&) [with _Tp = int; _Up = boost::test_tools::assertion::value_expr<std::optional<int>&>; std::__optional_relop_t<decltype ((declval<_Up>() == declval<_Tp>()))> = bool]'  
 1101 |     operator==(const _Up& __lhs, const optional<_Tp>& __rhs)  
      |     ^~~~~~~~  
In file included from /app/boost/include/boost/test/test_tools.hpp:53,  
                 from /app/boost/include/boost/test/unit_test.hpp:18,  
                 from <source>:2:  
/app/boost/include/boost/test/tools/assertion.hpp:227:5: note: candidate 2: 'boost::test_tools::assertion::binary_expr<ExprType, T, boost::test_tools::assertion::op::EQ<ValType, typename boost::test_tools::assertion::expression_base<ExprType, ValType>::RhsT<T>::type> > boost::test_tools::assertion::expression_base<ExprType, ValType>::operator==(T&&) [with T = std::optional<int>&; ExprType = boost::test_tools::assertion::value_expr<std::optional<int>&>; ValType = std::optional<int>; typename boost::test_tools::assertion::expression_base<ExprType, ValType>::RhsT<T>::type = std::optional<int>]'  
  227 |     BOOST_TEST_FOR_EACH_CONST_OP( ADD_OP_SUPPORT )  
      |     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /app/boost/include/boost/test/test_tools.hpp:52,  
                 from /app/boost/include/boost/test/unit_test.hpp:18,  
                 from <source>:2:  
<source>:8:5: error: request for member 'evaluate' in 'std::operator==<int, boost::test_tools::assertion::value_expr<std::optional<int>&> >(boost::test_tools::assertion::seed::operator->*(T&&) const [with T = std::optional<int>&](a), b)', which is of non-class type 'std::__optional_relop_t<boost::test_tools::assertion::binary_expr<boost::test_tools::assertion::value_expr<std::optional<int>&>, int, boost::test_tools::assertion::op::EQ<std::optional<int>, int, void> > >' {aka 'bool'}  
    8 |     BOOST_TEST(a==b);  
      |     ^~~~~~~~~~  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional: In instantiation of 'constexpr std::__optional_relop_t<decltype ((declval<_Up>() == declval<_Tp>()))> std::operator==(const _Up&, const std::optional<_Tp>&) [with _Tp = int; _Up = boost::test_tools::assertion::value_expr<std::optional<int>&>; std::__optional_relop_t<decltype ((declval<_Up>() == declval<_Tp>()))> = bool]':  
<source>:8:5:   required from here  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: error: no match for 'operator==' (operand types are 'const boost::test_tools::assertion::value_expr<std::optional<int>&>' and 'const int')  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /app/boost/include/boost/test/test_tools.hpp:53,  
                 from /app/boost/include/boost/test/unit_test.hpp:18,  
                 from <source>:2:  
/app/boost/include/boost/test/tools/assertion.hpp:227:5: note: candidate: 'boost::test_tools::assertion::binary_expr<ExprType, T, boost::test_tools::assertion::op::EQ<ValType, typename boost::test_tools::assertion::expression_base<ExprType, ValType>::RhsT<T>::type> > boost::test_tools::assertion::expression_base<ExprType, ValType>::operator==(T&&) [with T = const int&; ExprType = boost::test_tools::assertion::value_expr<std::optional<int>&>; ValType = std::optional<int>; typename boost::test_tools::assertion::expression_base<ExprType, ValType>::RhsT<T>::type = int]' <near match>  
  227 |     BOOST_TEST_FOR_EACH_CONST_OP( ADD_OP_SUPPORT )  
      |     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/app/boost/include/boost/test/tools/assertion.hpp:227:5: note:   passing 'const boost::test_tools::assertion::value_expr<std::optional<int>&>*' as 'this' argument discards qualifiers  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note: candidate: 'operator==(int, int)' <built-in>  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   conversion of argument 1 would be ill-formed:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: error: invalid user-defined conversion from 'const boost::test_tools::assertion::value_expr<std::optional<int>&>' to 'int' [-fpermissive]  
In file included from /app/boost/include/boost/test/test_tools.hpp:53,  
                 from /app/boost/include/boost/test/unit_test.hpp:18,  
                 from <source>:2:  
/app/boost/include/boost/test/tools/assertion.hpp:250:5: note: candidate is: 'boost::test_tools::assertion::expression_base<ExprType, ValType>::operator bool() [with ExprType = boost::test_tools::assertion::value_expr<std::optional<int>&>; ValType = std::optional<int>]' <near match>  
  250 |     operator bool()  
      |     ^~~~~~~~  
/app/boost/include/boost/test/tools/assertion.hpp:250:5: note:   passing 'const boost::test_tools::assertion::value_expr<std::optional<int>&>*' as 'this' argument discards qualifiers  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: error: passing 'const boost::test_tools::assertion::value_expr<std::optional<int>&>' as 'this' argument discards qualifiers [-fpermissive]  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /app/boost/include/boost/test/test_tools.hpp:53,  
                 from /app/boost/include/boost/test/unit_test.hpp:18,  
                 from <source>:2:  
/app/boost/include/boost/test/tools/assertion.hpp:250:5: note:   in call to 'boost::test_tools::assertion::expression_base<ExprType, ValType>::operator bool() [with ExprType = boost::test_tools::assertion::value_expr<std::optional<int>&>; ValType = std::optional<int>]'  
  250 |     operator bool()  
      |     ^~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/utility:70,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:36,  
                 from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/stl_pair.h:448:5: note: candidate: 'template<class _T1, class _T2> constexpr bool std::operator==(const std::pair<_T1, _T2>&, const std::pair<_T1, _T2>&)'  
  448 |     operator==(const pair<_T1, _T2>& __x, const pair<_T1, _T2>& __y)  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/stl_pair.h:448:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'const boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'const std::pair<_T1, _T2>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/stl_algobase.h:67,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/char_traits.h:39,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/string:40,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/stdexcept:39,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:38,  
                 from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/stl_iterator.h:325:5: note: candidate: 'template<class _Iterator> constexpr bool std::operator==(const std::reverse_iterator<_Iterator>&, const std::reverse_iterator<_Iterator>&)'  
  325 |     operator==(const reverse_iterator<_Iterator>& __x,  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/stl_iterator.h:325:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'const boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'const std::reverse_iterator<_Iterator>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/stl_algobase.h:67,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/char_traits.h:39,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/string:40,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/stdexcept:39,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:38,  
                 from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/stl_iterator.h:363:5: note: candidate: 'template<class _IteratorL, class _IteratorR> constexpr bool std::operator==(const std::reverse_iterator<_Iterator>&, const std::reverse_iterator<_IteratorR>&)'  
  363 |     operator==(const reverse_iterator<_IteratorL>& __x,  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/stl_iterator.h:363:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'const boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'const std::reverse_iterator<_Iterator>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/stl_algobase.h:67,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/char_traits.h:39,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/string:40,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/stdexcept:39,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:38,  
                 from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/stl_iterator.h:1136:5: note: candidate: 'template<class _IteratorL, class _IteratorR> constexpr bool std::operator==(const std::move_iterator<_IteratorL>&, const std::move_iterator<_IteratorR>&)'  
 1136 |     operator==(const move_iterator<_IteratorL>& __x,  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/stl_iterator.h:1136:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'const boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'const std::move_iterator<_IteratorL>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/stl_algobase.h:67,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/char_traits.h:39,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/string:40,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/stdexcept:39,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:38,  
                 from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/stl_iterator.h:1142:5: note: candidate: 'template<class _Iterator> constexpr bool std::operator==(const std::move_iterator<_IteratorL>&, const std::move_iterator<_IteratorL>&)'  
 1142 |     operator==(const move_iterator<_Iterator>& __x,  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/stl_iterator.h:1142:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'const boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'const std::move_iterator<_IteratorL>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/char_traits.h:40,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/string:40,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/stdexcept:39,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:38,  
                 from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/postypes.h:222:5: note: candidate: 'template<class _StateT> bool std::operator==(const std::fpos<_StateT>&, const std::fpos<_StateT>&)'  
  222 |     operator==(const fpos<_StateT>& __lhs, const fpos<_StateT>& __rhs)  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/postypes.h:222:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'const boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'const std::fpos<_StateT>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/string:41,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/stdexcept:39,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:38,  
                 from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/allocator.h:167:5: note: candidate: 'template<class _T1, class _T2> bool std::operator==(const std::allocator<_CharT>&, const std::allocator<_T2>&)'  
  167 |     operator==(const allocator<_T1>&, const allocator<_T2>&)  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/allocator.h:167:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'const boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'const std::allocator<_CharT>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/basic_string.h:48,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/string:55,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/stdexcept:39,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:38,  
                 from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/string_view:471:5: note: candidate: 'template<class _CharT, class _Traits> constexpr bool std::operator==(std::basic_string_view<_CharT, _Traits>, std::basic_string_view<_CharT, _Traits>)'  
  471 |     operator==(basic_string_view<_CharT, _Traits> __x,  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/string_view:471:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'std::basic_string_view<_CharT, _Traits>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/basic_string.h:48,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/string:55,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/stdexcept:39,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:38,  
                 from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/string_view:477:5: note: candidate: 'template<class _CharT, class _Traits> constexpr bool std::operator==(std::basic_string_view<_CharT, _Traits>, std::__detail::__idt<std::basic_string_view<_CharT, _Traits> >)'  
  477 |     operator==(basic_string_view<_CharT, _Traits> __x,  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/string_view:477:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'std::basic_string_view<_CharT, _Traits>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/basic_string.h:48,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/string:55,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/stdexcept:39,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:38,  
                 from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/string_view:483:5: note: candidate: 'template<class _CharT, class _Traits> constexpr bool std::operator==(std::__detail::__idt<std::basic_string_view<_CharT, _Traits> >, std::basic_string_view<_CharT, _Traits>)'  
  483 |     operator==(__detail::__idt<basic_string_view<_CharT, _Traits>> __x,  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/string_view:483:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   mismatched types 'std::basic_string_view<_CharT, _Traits>' and 'int'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/string:55,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/stdexcept:39,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:38,  
                 from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/basic_string.h:6149:5: note: candidate: 'template<class _CharT, class _Traits, class _Alloc> bool std::operator==(const std::__cxx11::basic_string<_CharT, _Traits, _Allocator>&, const std::__cxx11::basic_string<_CharT, _Traits, _Allocator>&)'  
 6149 |     operator==(const basic_string<_CharT, _Traits, _Alloc>& __lhs,  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/basic_string.h:6149:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'const boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'const std::__cxx11::basic_string<_CharT, _Traits, _Allocator>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/string:55,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/stdexcept:39,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:38,  
                 from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/basic_string.h:6157:5: note: candidate: 'template<class _CharT> typename __gnu_cxx::__enable_if<std::__is_char<_Tp>::__value, bool>::__type std::operator==(const std::__cxx11::basic_string<_CharT>&, const std::__cxx11::basic_string<_CharT>&)'  
 6157 |     operator==(const basic_string<_CharT>& __lhs,  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/basic_string.h:6157:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'const boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'const std::__cxx11::basic_string<_CharT>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/string:55,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/stdexcept:39,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:38,  
                 from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/basic_string.h:6171:5: note: candidate: 'template<class _CharT, class _Traits, class _Alloc> bool std::operator==(const _CharT*, const std::__cxx11::basic_string<_CharT, _Traits, _Allocator>&)'  
 6171 |     operator==(const _CharT* __lhs,  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/basic_string.h:6171:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   mismatched types 'const _CharT*' and 'boost::test_tools::assertion::value_expr<std::optional<int>&>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/string:55,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/stdexcept:39,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:38,  
                 from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/basic_string.h:6183:5: note: candidate: 'template<class _CharT, class _Traits, class _Alloc> bool std::operator==(const std::__cxx11::basic_string<_CharT, _Traits, _Allocator>&, const _CharT*)'  
 6183 |     operator==(const basic_string<_CharT, _Traits, _Alloc>& __lhs,  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/basic_string.h:6183:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'const boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'const std::__cxx11::basic_string<_CharT, _Traits, _Allocator>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:983:5: note: candidate: 'template<class _Tp, class _Up> constexpr std::__optional_relop_t<decltype ((declval<_Tp>() == declval<_Up>()))> std::operator==(const std::optional<_Tp>&, const std::optional<_Up>&)'  
  983 |     operator==(const optional<_Tp>& __lhs, const optional<_Up>& __rhs)  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:983:5: note:   template argument deduction/substitution failed:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'const boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'const std::optional<_Tp>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1034:5: note: candidate: 'template<class _Tp> constexpr bool std::operator==(const std::optional<_Tp>&, std::nullopt_t)'  
 1034 |     operator==(const optional<_Tp>& __lhs, nullopt_t) noexcept  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1034:5: note:   template argument deduction/substitution failed:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'const boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'const std::optional<_Tp>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1039:5: note: candidate: 'template<class _Tp> constexpr bool std::operator==(std::nullopt_t, const std::optional<_Tp>&)'  
 1039 |     operator==(nullopt_t, const optional<_Tp>& __rhs) noexcept  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1039:5: note:   template argument deduction/substitution failed:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   mismatched types 'const std::optional<_Tp>' and 'const int'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1095:5: note: candidate: 'template<class _Tp, class _Up> constexpr std::__optional_relop_t<decltype ((declval<_Tp>() == declval<_Up>()))> std::operator==(const std::optional<_Tp>&, const _Up&)'  
 1095 |     operator==(const optional<_Tp>& __lhs, const _Up& __rhs)  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1095:5: note:   template argument deduction/substitution failed:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'const boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'const std::optional<_Tp>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1101:5: note: candidate: 'template<class _Tp, class _Up> constexpr std::__optional_relop_t<decltype ((declval<_Up>() == declval<_Tp>()))> std::operator==(const _Up&, const std::optional<_Tp>&)'  
 1101 |     operator==(const _Up& __lhs, const optional<_Tp>& __rhs)  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1101:5: note:   template argument deduction/substitution failed:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   mismatched types 'const std::optional<_Tp>' and 'const int'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/ios_base.h:46,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/ios:42,  
                 from /app/boost/include/boost/test/tools/detail/print_helper.hpp:30,  
                 from /app/boost/include/boost/test/utils/lazy_ostream.hpp:16,  
                 from /app/boost/include/boost/test/tools/context.hpp:19,  
                 from /app/boost/include/boost/test/test_tools.hpp:42,  
                 from /app/boost/include/boost/test/unit_test.hpp:18,  
                 from <source>:2:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/system_error:292:3: note: candidate: 'bool std::operator==(const std::error_code&, const std::error_code&)'  
  292 |   operator==(const error_code& __lhs, const error_code& __rhs) noexcept  
      |   ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/system_error:292:32: note:   no known conversion for argument 1 from 'const boost::test_tools::assertion::value_expr<std::optional<int>&>' to 'const std::error_code&'  
  292 |   operator==(const error_code& __lhs, const error_code& __rhs) noexcept  
      |              ~~~~~~~~~~~~~~~~~~^~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/system_error:297:3: note: candidate: 'bool std::operator==(const std::error_code&, const std::error_condition&)'  
  297 |   operator==(const error_code& __lhs, const error_condition& __rhs) noexcept  
      |   ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/system_error:297:32: note:   no known conversion for argument 1 from 'const boost::test_tools::assertion::value_expr<std::optional<int>&>' to 'const std::error_code&'  
  297 |   operator==(const error_code& __lhs, const error_condition& __rhs) noexcept  
      |              ~~~~~~~~~~~~~~~~~~^~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/system_error:304:3: note: candidate: 'bool std::operator==(const std::error_condition&, const std::error_code&)'  
  304 |   operator==(const error_condition& __lhs, const error_code& __rhs) noexcept  
      |   ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/system_error:304:37: note:   no known conversion for argument 1 from 'const boost::test_tools::assertion::value_expr<std::optional<int>&>' to 'const std::error_condition&'  
  304 |   operator==(const error_condition& __lhs, const error_code& __rhs) noexcept  
      |              ~~~~~~~~~~~~~~~~~~~~~~~^~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/system_error:311:3: note: candidate: 'bool std::operator==(const std::error_condition&, const std::error_condition&)'  
  311 |   operator==(const error_condition& __lhs,  
      |   ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/system_error:311:37: note:   no known conversion for argument 1 from 'const boost::test_tools::assertion::value_expr<std::optional<int>&>' to 'const std::error_condition&'  
  311 |   operator==(const error_condition& __lhs,  
      |              ~~~~~~~~~~~~~~~~~~~~~~~^~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/locale_facets.h:48,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/basic_ios.h:37,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/ios:44,  
                 from /app/boost/include/boost/test/tools/detail/print_helper.hpp:30,  
                 from /app/boost/include/boost/test/utils/lazy_ostream.hpp:16,  
                 from /app/boost/include/boost/test/tools/context.hpp:19,  
                 from /app/boost/include/boost/test/test_tools.hpp:42,  
                 from /app/boost/include/boost/test/unit_test.hpp:18,  
                 from <source>:2:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/streambuf_iterator.h:208:5: note: candidate: 'template<class _CharT, class _Traits> bool std::operator==(const std::istreambuf_iterator<_CharT, _Traits>&, const std::istreambuf_iterator<_CharT, _Traits>&)'  
  208 |     operator==(const istreambuf_iterator<_CharT, _Traits>& __a,  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/streambuf_iterator.h:208:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'const boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'const std::istreambuf_iterator<_CharT, _Traits>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/tuple:39,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/unique_ptr.h:37,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/memory:80,  
                 from /app/boost/include/boost/smart_ptr/detail/sp_counted_impl.hpp:27,  
                 from /app/boost/include/boost/smart_ptr/detail/shared_count.hpp:23,  
                 from /app/boost/include/boost/smart_ptr/shared_ptr.hpp:17,  
                 from /app/boost/include/boost/shared_ptr.hpp:17,  
                 from /app/boost/include/boost/test/tools/assertion_result.hpp:21,  
                 from /app/boost/include/boost/test/tools/old/impl.hpp:20,  
                 from /app/boost/include/boost/test/test_tools.hpp:46,  
                 from /app/boost/include/boost/test/unit_test.hpp:18,  
                 from <source>:2:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/array:252:5: note: candidate: 'template<class _Tp, long unsigned int _Nm> bool std::operator==(const std::array<_Tp, _Nm>&, const std::array<_Tp, _Nm>&)'  
  252 |     operator==(const array<_Tp, _Nm>& __one, const array<_Tp, _Nm>& __two)  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/array:252:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'const boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'const std::array<_Tp, _Nm>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/unique_ptr.h:37,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/memory:80,  
                 from /app/boost/include/boost/smart_ptr/detail/sp_counted_impl.hpp:27,  
                 from /app/boost/include/boost/smart_ptr/detail/shared_count.hpp:23,  
                 from /app/boost/include/boost/smart_ptr/shared_ptr.hpp:17,  
                 from /app/boost/include/boost/shared_ptr.hpp:17,  
                 from /app/boost/include/boost/test/tools/assertion_result.hpp:21,  
                 from /app/boost/include/boost/test/tools/old/impl.hpp:20,  
                 from /app/boost/include/boost/test/test_tools.hpp:46,  
                 from /app/boost/include/boost/test/unit_test.hpp:18,  
                 from <source>:2:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/tuple:1419:5: note: candidate: 'template<class ... _TElements, class ... _UElements> constexpr bool std::operator==(const std::tuple<_Tps ...>&, const std::tuple<_Elements ...>&)'  
 1419 |     operator==(const tuple<_TElements...>& __t,  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/tuple:1419:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'const boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'const std::tuple<_Tps ...>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/memory:80,  
                 from /app/boost/include/boost/smart_ptr/detail/sp_counted_impl.hpp:27,  
                 from /app/boost/include/boost/smart_ptr/detail/shared_count.hpp:23,  
                 from /app/boost/include/boost/smart_ptr/shared_ptr.hpp:17,  
                 from /app/boost/include/boost/shared_ptr.hpp:17,  
                 from /app/boost/include/boost/test/tools/assertion_result.hpp:21,  
                 from /app/boost/include/boost/test/tools/old/impl.hpp:20,  
                 from /app/boost/include/boost/test/test_tools.hpp:46,  
                 from /app/boost/include/boost/test/unit_test.hpp:18,  
                 from <source>:2:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/unique_ptr.h:715:5: note: candidate: 'template<class _Tp, class _Dp, class _Up, class _Ep> bool std::operator==(const std::unique_ptr<_Tp, _Dp>&, const std::unique_ptr<_Up, _Ep>&)'  
  715 |     operator==(const unique_ptr<_Tp, _Dp>& __x,  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/unique_ptr.h:715:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'const boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'const std::unique_ptr<_Tp, _Dp>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/memory:80,  
                 from /app/boost/include/boost/smart_ptr/detail/sp_counted_impl.hpp:27,  
                 from /app/boost/include/boost/smart_ptr/detail/shared_count.hpp:23,  
                 from /app/boost/include/boost/smart_ptr/shared_ptr.hpp:17,  
                 from /app/boost/include/boost/shared_ptr.hpp:17,  
                 from /app/boost/include/boost/test/tools/assertion_result.hpp:21,  
                 from /app/boost/include/boost/test/tools/old/impl.hpp:20,  
                 from /app/boost/include/boost/test/test_tools.hpp:46,  
                 from /app/boost/include/boost/test/unit_test.hpp:18,  
                 from <source>:2:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/unique_ptr.h:721:5: note: candidate: 'template<class _Tp, class _Dp> bool std::operator==(const std::unique_ptr<_Tp, _Dp>&, std::nullptr_t)'  
  721 |     operator==(const unique_ptr<_Tp, _Dp>& __x, nullptr_t) noexcept  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/unique_ptr.h:721:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'const boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'const std::unique_ptr<_Tp, _Dp>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/memory:80,  
                 from /app/boost/include/boost/smart_ptr/detail/sp_counted_impl.hpp:27,  
                 from /app/boost/include/boost/smart_ptr/detail/shared_count.hpp:23,  
                 from /app/boost/include/boost/smart_ptr/shared_ptr.hpp:17,  
                 from /app/boost/include/boost/shared_ptr.hpp:17,  
                 from /app/boost/include/boost/test/tools/assertion_result.hpp:21,  
                 from /app/boost/include/boost/test/tools/old/impl.hpp:20,  
                 from /app/boost/include/boost/test/test_tools.hpp:46,  
                 from /app/boost/include/boost/test/unit_test.hpp:18,  
                 from <source>:2:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/unique_ptr.h:726:5: note: candidate: 'template<class _Tp, class _Dp> bool std::operator==(std::nullptr_t, const std::unique_ptr<_Tp, _Dp>&)'  
  726 |     operator==(nullptr_t, const unique_ptr<_Tp, _Dp>& __x) noexcept  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/unique_ptr.h:726:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   mismatched types 'const std::unique_ptr<_Tp, _Dp>' and 'const int'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/shared_ptr.h:52,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/memory:81,  
                 from /app/boost/include/boost/smart_ptr/detail/sp_counted_impl.hpp:27,  
                 from /app/boost/include/boost/smart_ptr/detail/shared_count.hpp:23,  
                 from /app/boost/include/boost/smart_ptr/shared_ptr.hpp:17,  
                 from /app/boost/include/boost/shared_ptr.hpp:17,  
                 from /app/boost/include/boost/test/tools/assertion_result.hpp:21,  
                 from /app/boost/include/boost/test/tools/old/impl.hpp:20,  
                 from /app/boost/include/boost/test/test_tools.hpp:46,  
                 from /app/boost/include/boost/test/unit_test.hpp:18,  
                 from <source>:2:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/shared_ptr_base.h:1412:5: note: candidate: 'template<class _Tp1, class _Tp2, __gnu_cxx::_Lock_policy _Lp> bool std::operator==(const std::__shared_ptr<_Tp1, _Lp>&, const std::__shared_ptr<_Tp2, _Lp>&)'  
 1412 |     operator==(const __shared_ptr<_Tp1, _Lp>& __a,  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/shared_ptr_base.h:1412:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'const boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'const std::__shared_ptr<_Tp1, _Lp>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/shared_ptr.h:52,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/memory:81,  
                 from /app/boost/include/boost/smart_ptr/detail/sp_counted_impl.hpp:27,  
                 from /app/boost/include/boost/smart_ptr/detail/shared_count.hpp:23,  
                 from /app/boost/include/boost/smart_ptr/shared_ptr.hpp:17,  
                 from /app/boost/include/boost/shared_ptr.hpp:17,  
                 from /app/boost/include/boost/test/tools/assertion_result.hpp:21,  
                 from /app/boost/include/boost/test/tools/old/impl.hpp:20,  
                 from /app/boost/include/boost/test/test_tools.hpp:46,  
                 from /app/boost/include/boost/test/unit_test.hpp:18,  
                 from <source>:2:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/shared_ptr_base.h:1418:5: note: candidate: 'template<class _Tp, __gnu_cxx::_Lock_policy _Lp> bool std::operator==(const std::__shared_ptr<_Tp, _Lp>&, std::nullptr_t)'  
 1418 |     operator==(const __shared_ptr<_Tp, _Lp>& __a, nullptr_t) noexcept  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/shared_ptr_base.h:1418:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'const boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'const std::__shared_ptr<_Tp, _Lp>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/shared_ptr.h:52,  
                 from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/memory:81,  
                 from /app/boost/include/boost/smart_ptr/detail/sp_counted_impl.hpp:27,  
                 from /app/boost/include/boost/smart_ptr/detail/shared_count.hpp:23,  
                 from /app/boost/include/boost/smart_ptr/shared_ptr.hpp:17,  
                 from /app/boost/include/boost/shared_ptr.hpp:17,  
                 from /app/boost/include/boost/test/tools/assertion_result.hpp:21,  
                 from /app/boost/include/boost/test/tools/old/impl.hpp:20,  
                 from /app/boost/include/boost/test/test_tools.hpp:46,  
                 from /app/boost/include/boost/test/unit_test.hpp:18,  
                 from <source>:2:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/shared_ptr_base.h:1423:5: note: candidate: 'template<class _Tp, __gnu_cxx::_Lock_policy _Lp> bool std::operator==(std::nullptr_t, const std::__shared_ptr<_Tp, _Lp>&)'  
 1423 |     operator==(nullptr_t, const __shared_ptr<_Tp, _Lp>& __a) noexcept  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/shared_ptr_base.h:1423:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   mismatched types 'const std::__shared_ptr<_Tp, _Lp>' and 'const int'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/memory:81,  
                 from /app/boost/include/boost/smart_ptr/detail/sp_counted_impl.hpp:27,  
                 from /app/boost/include/boost/smart_ptr/detail/shared_count.hpp:23,  
                 from /app/boost/include/boost/smart_ptr/shared_ptr.hpp:17,  
                 from /app/boost/include/boost/shared_ptr.hpp:17,  
                 from /app/boost/include/boost/test/tools/assertion_result.hpp:21,  
                 from /app/boost/include/boost/test/tools/old/impl.hpp:20,  
                 from /app/boost/include/boost/test/test_tools.hpp:46,  
                 from /app/boost/include/boost/test/unit_test.hpp:18,  
                 from <source>:2:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/shared_ptr.h:383:5: note: candidate: 'template<class _Tp, class _Up> bool std::operator==(const std::shared_ptr<_Tp>&, const std::shared_ptr<_Tp>&)'  
  383 |     operator==(const shared_ptr<_Tp>& __a, const shared_ptr<_Up>& __b) noexcept  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/shared_ptr.h:383:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'const boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'const std::shared_ptr<_Tp>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/memory:81,  
                 from /app/boost/include/boost/smart_ptr/detail/sp_counted_impl.hpp:27,  
                 from /app/boost/include/boost/smart_ptr/detail/shared_count.hpp:23,  
                 from /app/boost/include/boost/smart_ptr/shared_ptr.hpp:17,  
                 from /app/boost/include/boost/shared_ptr.hpp:17,  
                 from /app/boost/include/boost/test/tools/assertion_result.hpp:21,  
                 from /app/boost/include/boost/test/tools/old/impl.hpp:20,  
                 from /app/boost/include/boost/test/test_tools.hpp:46,  
                 from /app/boost/include/boost/test/unit_test.hpp:18,  
                 from <source>:2:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/shared_ptr.h:388:5: note: candidate: 'template<class _Tp> bool std::operator==(const std::shared_ptr<_Tp>&, std::nullptr_t)'  
  388 |     operator==(const shared_ptr<_Tp>& __a, nullptr_t) noexcept  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/shared_ptr.h:388:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   'const boost::test_tools::assertion::value_expr<std::optional<int>&>' is not derived from 'const std::shared_ptr<_Tp>'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
In file included from /cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/memory:81,  
                 from /app/boost/include/boost/smart_ptr/detail/sp_counted_impl.hpp:27,  
                 from /app/boost/include/boost/smart_ptr/detail/shared_count.hpp:23,  
                 from /app/boost/include/boost/smart_ptr/shared_ptr.hpp:17,  
                 from /app/boost/include/boost/shared_ptr.hpp:17,  
                 from /app/boost/include/boost/test/tools/assertion_result.hpp:21,  
                 from /app/boost/include/boost/test/tools/old/impl.hpp:20,  
                 from /app/boost/include/boost/test/test_tools.hpp:46,  
                 from /app/boost/include/boost/test/unit_test.hpp:18,  
                 from <source>:2:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/shared_ptr.h:393:5: note: candidate: 'template<class _Tp> bool std::operator==(std::nullptr_t, const std::shared_ptr<_Tp>&)'  
  393 |     operator==(nullptr_t, const shared_ptr<_Tp>& __a) noexcept  
      |     ^~~~~~~~  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/bits/shared_ptr.h:393:5: note:   template argument deduction/substitution failed:  
In file included from <source>:1:  
/cefs/22/22e6cdc013c8541ce3d1548e_consolidated/compilers_c++_x86_gcc_9.5.0/include/c++/9.5.0/optional:1103:29: note:   mismatched types 'const std::shared_ptr<_Tp>' and 'const int'  
 1103 |     { return __rhs && __lhs == *__rhs; }  
      |                       ~~~~~~^~~~~~~~~  
  
</details>

---

## Comment 1

> Username: mborland  
> Created at: 2026-01-23 13:43:31 UTC  
> Url: https://github.com/boostorg/test/issues/475#issuecomment-3790297282  

That is odd, because the only compatibility macros in the pointed to files are for C++11 support. Fortunately it can be worked around with an extra set of parenthesis.  
  
```c++  
#include <optional>  
#include <boost/test/unit_test.hpp>  
  
BOOST_TEST_DONT_PRINT_LOG_VALUE(std::optional<int>)  
  
void foo() {  
    std::optional<int> a,b;  
    BOOST_TEST((a==b));  
}  
```  
  
See: https://godbolt.org/z/oMEWevMbs

---

## Comment 2

> Username: Flamefire  
> Created at: 2026-01-23 16:43:25 UTC  
> Updated at: 2026-01-23 16:48:55 UTC  
> Url: https://github.com/boostorg/test/issues/475#issuecomment-3791186099  

`BOOST_TEST((a==b));` is like `BOOST_CHECK(a==b)` and looses the printing capabilities.  
  
The interesting diagnostic from GCC:  
> warning: ISO C++ says that these are ambiguous, even though the worst conversion for the first is better than the worst conversion for the second:  
  
What I find a bit confusing is this type: `boost::test_tools::assertion::binary_expr<boost::test_tools::assertion::value_expr<std::optional<int>&>, int, boost::test_tools::assertion::op::EQ<std::optional<int>, int, void> >`    
Shouldn't that be `EQ<optional,optional>`?
