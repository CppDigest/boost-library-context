# #312 - [flat_map] std::allocator::is_always_equal is deprecated [Closed]

> Username: pps83  
> Created at: 2025-08-30 05:50:07 UTC  
> Updated at: 2025-10-27 08:08:04 UTC  
> Closed at: 2025-10-27 08:08:04 UTC  
> Url: https://github.com/boostorg/container/issues/312  

If I try to use `std::vector` for storage of flat_map I get tons of warnings from from [intel 2025](https://marketplace.visualstudio.com/items?itemName=intel-corporation.dpcpponline) compiler for visual studio when compiling for c++20.  
  
Here's sample code:  
  
```  
#include <boost/container/flat_map.hpp>  
#include <vector>  
  
#if 0  
typedef boost::container::flat_map<int, long> EodData;  
#else  
typedef boost::container::flat_map<int, long, std::less<int>, std::vector<std::pair<int, long>>> EodData;  
#endif  
  
void test(int date, const std::vector<EodData::value_type>& eodData);  
```  
  
Note, above, if I do not use std::vector then there are no warnings. The biggest issue is that the warning comes from simply using type declarations that completely overwhelms my code with endless warnings when I build the project.  
  
These are the warnings:  
  
```  
Build started at 07:12...  
1>------ Build started: Project: common, Configuration: Debug x64 ------  
1>In file included from D:\work-pps\src\DataSourceEod.cpp:6:  
1>In file included from D:\work-pps\boost_1_89_0\boost\container\flat_map.hpp:24:  
1>D:\work-pps\boost_1_89_0\boost\container\allocator_traits.hpp(240,48): : warning : 'is_always_equal' is deprecated: warning STL4033: std::allocator::is_always_equal is deprecated in C++20 by LWG-3170. Prefer std::allocator_traits<allocator<T>>::is_always_equal. You can define _SILENCE_CXX20_IS_ALWAYS_EQUAL_DEPRECATION_WARNING or _SILENCE_ALL_CXX20_DEPRECATION_WARNINGS to suppress this warning. [-Wdeprecated-declarations]  
1>  240 | BOOST_INTRUSIVE_INSTANTIATE_DEFAULT_TYPE_TMPLT(is_always_equal)  
1>      |                                                ^  
1>D:\work-pps\boost_1_89_0\boost\container\allocator_traits.hpp(240,1): note: while substituting explicitly-specified template arguments into function template 'test'  
1>  240 | BOOST_INTRUSIVE_INSTANTIATE_DEFAULT_TYPE_TMPLT(is_always_equal)  
1>      | ^  
1>D:\work-pps\boost_1_89_0\boost\intrusive\detail\mpl.hpp(105,46): note: expanded from macro 'BOOST_INTRUSIVE_INSTANTIATE_DEFAULT_TYPE_TMPLT'  
1>  105 |       static const bool value = (1 == sizeof(test<T>(0, 0)));     \  
1>      |                                              ^  
1>D:\work-pps\boost_1_89_0\boost\container\allocator_traits.hpp(240,1): note: in instantiation of template class 'boost::container::dtl::boost_intrusive_has_type_is_always_equal<std::allocator<std::pair<int, long>>>' requested here  
1>D:\work-pps\boost_1_89_0\boost\intrusive\detail\mpl.hpp(115,15): note: expanded from macro 'BOOST_INTRUSIVE_INSTANTIATE_DEFAULT_TYPE_TMPLT'  
1>  115 |             < boost_intrusive_has_type_ ## TNAME<T>::value        \  
1>      |               ^  
1><scratch space>(30,1): note: expanded from here  
1>   30 | boost_intrusive_has_type_is_always_equal  
1>      | ^  
1>D:\work-pps\boost_1_89_0\boost\container\allocator_traits.hpp(366,15): note: in instantiation of template class 'boost::container::dtl::boost_intrusive_default_type_is_always_equal<std::allocator<std::pair<int, long>>, boost::move_detail::is_empty<std::allocator<std::pair<int, long>>>>' requested here  
1>  366 |       typedef BOOST_INTRUSIVE_OBTAIN_TYPE_WITH_DEFAULT(boost::container::dtl::, Allocator,  
1>      |               ^  
1>D:\work-pps\boost_1_89_0\boost\intrusive\detail\mpl.hpp(122,10): note: expanded from macro 'BOOST_INTRUSIVE_OBTAIN_TYPE_WITH_DEFAULT'  
1>  122 |          boost_intrusive_default_type_ ## TNAME< T, TIMPL >::type                            \  
1>      |          ^  
1><scratch space>(48,1): note: expanded from here  
1>   48 | boost_intrusive_default_type_is_always_equal  
1>      | ^  
1>D:\work-pps\boost_1_89_0\boost\container\detail\flat_tree.hpp(601,64): note: in instantiation of template class 'boost::container::allocator_traits<std::allocator<std::pair<int, long>>>' requested here  
1>  601 |    BOOST_CONTAINER_STATIC_ASSERT((is_same<value_type, typename allocator_traits_type::value_type>::value));  
1>      |                                                                ^  
1>D:\work-pps\boost_1_89_0\boost\container\flat_map.hpp(178,16): note: in instantiation of template class 'boost::container::dtl::flat_tree<std::pair<int, long>, boost::container::dtl::select1st<int>, std::less<int>, std::vector<std::pair<int, long>>>' requested here  
1>  178 |    impl_tree_t m_flat_tree;  // flat tree representing flat_map  
1>      |                ^  
1>D:\work-pps\src\DataSourceEod.cpp(15,39): note: in instantiation of template class 'boost::container::flat_map<int, long, std::less<int>, std::vector<std::pair<int, long>>>' requested here  
1>   15 | void test(int date, const std::vector<EodData::value_type>& eodData);  
1>      |                                       ^  
1>d:\Program Files\Microsoft Visual Studio\2022\Preview\VC\Tools\MSVC\14.44.35207\include\xmemory(957,27): note: 'is_always_equal' has been explicitly marked deprecated here  
1>  957 |     using is_always_equal _CXX20_DEPRECATE_IS_ALWAYS_EQUAL = true_type;  
1>      |                           ^  
1>d:\Program Files\Microsoft Visual Studio\2022\Preview\VC\Tools\MSVC\14.44.35207\include\yvals_core.h(1368,7): note: expanded from macro '_CXX20_DEPRECATE_IS_ALWAYS_EQUAL'  
1> 1368 |     [[deprecated("warning STL4033: "                                                    \  
1>      |       ^  
1>In file included from D:\work-pps\src\DataSourceEod.cpp:6:  
1>In file included from D:\work-pps\boost_1_89_0\boost\container\flat_map.hpp:24:  
1>D:\work-pps\boost_1_89_0\boost\container\allocator_traits.hpp(240,48): : warning : 'is_always_equal' is deprecated: warning STL4033: std::allocator::is_always_equal is deprecated in C++20 by LWG-3170. Prefer std::allocator_traits<allocator<T>>::is_always_equal. You can define _SILENCE_CXX20_IS_ALWAYS_EQUAL_DEPRECATION_WARNING or _SILENCE_ALL_CXX20_DEPRECATION_WARNINGS to suppress this warning. [-Wdeprecated-declarations]  
1>  240 | BOOST_INTRUSIVE_INSTANTIATE_DEFAULT_TYPE_TMPLT(is_always_equal)  
1>      |                                                ^  
1>D:\work-pps\boost_1_89_0\boost\container\allocator_traits.hpp(240,1): note: while substituting deduced template arguments into function template 'test' [with X = std::allocator<std::pair<int, long>>]  
1>  240 | BOOST_INTRUSIVE_INSTANTIATE_DEFAULT_TYPE_TMPLT(is_always_equal)  
1>      | ^  
1>D:\work-pps\boost_1_89_0\boost\intrusive\detail\mpl.hpp(105,46): note: expanded from macro 'BOOST_INTRUSIVE_INSTANTIATE_DEFAULT_TYPE_TMPLT'  
1>  105 |       static const bool value = (1 == sizeof(test<T>(0, 0)));     \  
1>      |                                              ^  
1>D:\work-pps\boost_1_89_0\boost\container\allocator_traits.hpp(240,1): note: in instantiation of template class 'boost::container::dtl::boost_intrusive_has_type_is_always_equal<std::allocator<std::pair<int, long>>>' requested here  
1>D:\work-pps\boost_1_89_0\boost\intrusive\detail\mpl.hpp(115,15): note: expanded from macro 'BOOST_INTRUSIVE_INSTANTIATE_DEFAULT_TYPE_TMPLT'  
1>  115 |             < boost_intrusive_has_type_ ## TNAME<T>::value        \  
1>      |               ^  
1><scratch space>(30,1): note: expanded from here  
1>   30 | boost_intrusive_has_type_is_always_equal  
1>      | ^  
1>D:\work-pps\boost_1_89_0\boost\container\allocator_traits.hpp(366,15): note: in instantiation of template class 'boost::container::dtl::boost_intrusive_default_type_is_always_equal<std::allocator<std::pair<int, long>>, boost::move_detail::is_empty<std::allocator<std::pair<int, long>>>>' requested here  
1>  366 |       typedef BOOST_INTRUSIVE_OBTAIN_TYPE_WITH_DEFAULT(boost::container::dtl::, Allocator,  
1>      |               ^  
1>D:\work-pps\boost_1_89_0\boost\intrusive\detail\mpl.hpp(122,10): note: expanded from macro 'BOOST_INTRUSIVE_OBTAIN_TYPE_WITH_DEFAULT'  
1>  122 |          boost_intrusive_default_type_ ## TNAME< T, TIMPL >::type                            \  
1>      |          ^  
1><scratch space>(48,1): note: expanded from here  
1>   48 | boost_intrusive_default_type_is_always_equal  
1>      | ^  
1>D:\work-pps\boost_1_89_0\boost\container\detail\flat_tree.hpp(601,64): note: in instantiation of template class 'boost::container::allocator_traits<std::allocator<std::pair<int, long>>>' requested here  
1>  601 |    BOOST_CONTAINER_STATIC_ASSERT((is_same<value_type, typename allocator_traits_type::value_type>::value));  
1>      |                                                                ^  
1>D:\work-pps\boost_1_89_0\boost\container\flat_map.hpp(178,16): note: in instantiation of template class 'boost::container::dtl::flat_tree<std::pair<int, long>, boost::container::dtl::select1st<int>, std::less<int>, std::vector<std::pair<int, long>>>' requested here  
1>  178 |    impl_tree_t m_flat_tree;  // flat tree representing flat_map  
1>      |                ^  
1>D:\work-pps\src\DataSourceEod.cpp(15,39): note: in instantiation of template class 'boost::container::flat_map<int, long, std::less<int>, std::vector<std::pair<int, long>>>' requested here  
1>   15 | void test(int date, const std::vector<EodData::value_type>& eodData);  
1>      |                                       ^  
1>d:\Program Files\Microsoft Visual Studio\2022\Preview\VC\Tools\MSVC\14.44.35207\include\xmemory(957,27): note: 'is_always_equal' has been explicitly marked deprecated here  
1>  957 |     using is_always_equal _CXX20_DEPRECATE_IS_ALWAYS_EQUAL = true_type;  
1>      |                           ^  
1>d:\Program Files\Microsoft Visual Studio\2022\Preview\VC\Tools\MSVC\14.44.35207\include\yvals_core.h(1368,7): note: expanded from macro '_CXX20_DEPRECATE_IS_ALWAYS_EQUAL'  
1> 1368 |     [[deprecated("warning STL4033: "                                                    \  
1>      |       ^  
1>In file included from D:\work-pps\src\DataSourceEod.cpp:6:  
1>In file included from D:\work-pps\boost_1_89_0\boost\container\flat_map.hpp:24:  
1>D:\work-pps\boost_1_89_0\boost\container\allocator_traits.hpp(240,48): : warning : 'is_always_equal' is deprecated: warning STL4033: std::allocator::is_always_equal is deprecated in C++20 by LWG-3170. Prefer std::allocator_traits<allocator<T>>::is_always_equal. You can define _SILENCE_CXX20_IS_ALWAYS_EQUAL_DEPRECATION_WARNING or _SILENCE_ALL_CXX20_DEPRECATION_WARNINGS to suppress this warning. [-Wdeprecated-declarations]  
1>  240 | BOOST_INTRUSIVE_INSTANTIATE_DEFAULT_TYPE_TMPLT(is_always_equal)  
1>      |                                                ^  
1>D:\work-pps\boost_1_89_0\boost\container\allocator_traits.hpp(366,15): note: in instantiation of template class 'boost::container::dtl::boost_intrusive_default_type_is_always_equal<std::allocator<std::pair<int, long>>, boost::move_detail::is_empty<std::allocator<std::pair<int, long>>>>' requested here  
1>  366 |       typedef BOOST_INTRUSIVE_OBTAIN_TYPE_WITH_DEFAULT(boost::container::dtl::, Allocator,  
1>      |               ^  
1>D:\work-pps\boost_1_89_0\boost\intrusive\detail\mpl.hpp(122,10): note: expanded from macro 'BOOST_INTRUSIVE_OBTAIN_TYPE_WITH_DEFAULT'  
1>  122 |          boost_intrusive_default_type_ ## TNAME< T, TIMPL >::type                            \  
1>      |          ^  
1><scratch space>(48,1): note: expanded from here  
1>   48 | boost_intrusive_default_type_is_always_equal  
1>      | ^  
1>D:\work-pps\boost_1_89_0\boost\container\detail\flat_tree.hpp(601,64): note: in instantiation of template class 'boost::container::allocator_traits<std::allocator<std::pair<int, long>>>' requested here  
1>  601 |    BOOST_CONTAINER_STATIC_ASSERT((is_same<value_type, typename allocator_traits_type::value_type>::value));  
1>      |                                                                ^  
1>D:\work-pps\boost_1_89_0\boost\container\flat_map.hpp(178,16): note: in instantiation of template class 'boost::container::dtl::flat_tree<std::pair<int, long>, boost::container::dtl::select1st<int>, std::less<int>, std::vector<std::pair<int, long>>>' requested here  
1>  178 |    impl_tree_t m_flat_tree;  // flat tree representing flat_map  
1>      |                ^  
1>D:\work-pps\src\DataSourceEod.cpp(15,39): note: in instantiation of template class 'boost::container::flat_map<int, long, std::less<int>, std::vector<std::pair<int, long>>>' requested here  
1>   15 | void test(int date, const std::vector<EodData::value_type>& eodData);  
1>      |                                       ^  
1>d:\Program Files\Microsoft Visual Studio\2022\Preview\VC\Tools\MSVC\14.44.35207\include\xmemory(957,27): note: 'is_always_equal' has been explicitly marked deprecated here  
1>  957 |     using is_always_equal _CXX20_DEPRECATE_IS_ALWAYS_EQUAL = true_type;  
1>      |                           ^  
1>d:\Program Files\Microsoft Visual Studio\2022\Preview\VC\Tools\MSVC\14.44.35207\include\yvals_core.h(1368,7): note: expanded from macro '_CXX20_DEPRECATE_IS_ALWAYS_EQUAL'  
1> 1368 |     [[deprecated("warning STL4033: "                                                    \  
1>      |       ^  
1>3 warnings generated.  
1>Done building project "common.vcxproj".  
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========  
========== Build completed at 07:12 and took 01.332 seconds ==========  
```  
  
sample project:   
  
[test-flat-map.zip](https://github.com/user-attachments/files/22056596/test-flat-map.zip)

---

## Comment 1

> Username: pps83  
> Created at: 2025-08-30 06:00:13 UTC  
> Url: https://github.com/boostorg/container/issues/312#issuecomment-3238981869  

The other issue. Simply including flat_map.hpp with VS compiler with all warnings enabled in VS2022 results in warnings:  
```  
#include <boost/container/flat_map.hpp>  
```  
warnings:  
```  
1>test-flat-map.cpp  
1>D:\work-pps\boost_1_89_0\boost\move\detail\std_ns_end.hpp(15,21): warning C5031: #pragma warning(pop): likely mismatch, popping warning state pushed in different file  
1>(compiling source file 'test-flat-map.cpp')  
1>    D:\work-pps\boost_1_89_0\boost\move\detail\std_ns_begin.hpp(38,21):  
1>    #pragma warning(push)  
1>D:\work-pps\boost_1_89_0\boost\move\detail\type_traits.hpp(1125,9): warning C4820: 'boost::move_detail::alignment_of_hack<T>': '7' bytes padding added after data member 'boost::move_detail::alignment_of_hack<T>::c'  
```  
.... very long list of `#pragma warning(pop): likely mismatch, popping warning state pushed in different file`

---

## Comment 2

> Username: igaztanaga  
> Created at: 2025-10-27 07:56:43 UTC  
> Url: https://github.com/boostorg/container/issues/312#issuecomment-3449946279  

Regarding the last issue: /Wall is not supported by the library, and Microsoft does not recommend it (/W4 is the recommended level) as it activates warnings that are off by default for a good reason. Too much noise to be useful.  
  
Regarding the original issue (-Wdeprecated-declaration), yesterday downloaded the Intel compiler, integrated in my Visual Studio environment and reproduced it. A selective commit will disable the warning as done with similar reports (deprecations from allocator interface).
