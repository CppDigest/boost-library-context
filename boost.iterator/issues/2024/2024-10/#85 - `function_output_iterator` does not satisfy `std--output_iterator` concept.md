# #85 - `function_output_iterator` does not satisfy `std::output_iterator` concept [Closed]

> Username: gogagum  
> Created at: 2024-10-10 14:13:50 UTC  
> Updated at: 2024-10-10 21:30:22 UTC  
> Closed at: 2024-10-10 21:30:22 UTC  
> Url: https://github.com/boostorg/iterator/issues/85  

Example:  
  
```  
#include <iterator>  
  
#include <boost/iterator/function_output_iterator.hpp>  
  
int main() {  
    static_assert(std::output_iterator<decltype(boost::function_output_iterator([](int p) {  })), int>);  
}  
```  
  
`gcc` output:  
  
```  
<source>: In function 'int main()':  
<source>:6:24: error: static assertion failed  
    6 |     static_assert(std::output_iterator<decltype(boost::function_output_iterator([](int p) {  })), int>);  
      |                   ~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
<source>:6:24: note: constraints not satisfied  
In file included from /opt/compiler-explorer/gcc-14.2.0/include/c++/14.2.0/bits/stl_iterator_base_types.h:71,  
                 from /opt/compiler-explorer/gcc-14.2.0/include/c++/14.2.0/iterator:61,  
                 from <source>:1:  
/opt/compiler-explorer/gcc-14.2.0/include/c++/14.2.0/bits/iterator_concepts.h:619:13:   required for the satisfaction of 'weakly_incrementable<_Iter>' [with _Iter = boost::iterators::function_output_iterator<main::._anon_116>]  
/opt/compiler-explorer/gcc-14.2.0/include/c++/14.2.0/bits/iterator_concepts.h:633:13:   required for the satisfaction of 'input_or_output_iterator<_Iter>' [with _Iter = boost::iterators::function_output_iterator<main::._anon_116>]  
/opt/compiler-explorer/gcc-14.2.0/include/c++/14.2.0/bits/iterator_concepts.h:620:10:   in requirements with '_Iter __i' [with _Iter = boost::iterators::function_output_iterator<main::._anon_116>]  
/opt/compiler-explorer/gcc-14.2.0/include/c++/14.2.0/bits/iterator_concepts.h:623:18: note: nested requirement '__is_signed_integer_like<typename std::__detail::__iter_traits_impl<typename std::remove_cvref<_Tp>::type, std::incrementable_traits<typename std::remove_cvref<_Tp>::type> >::type::difference_type>' is not satisfied  
  623 |         requires __detail::__is_signed_integer_like<iter_difference_t<_Iter>>;  
      |         ~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cc1plus: note: set '-fconcepts-diagnostics-depth=' to at least 2 for more detail  
Compiler returned: 1  
```  
  
This issue makes it impossible to use the iterator with constrained algorithms from standard library.
